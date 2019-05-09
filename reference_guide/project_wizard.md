---
title: Project Wizard. Adding Support for Creating New Project Types.
---

##项目向导


使用项目向导可以使用

[RedLine SmallTalk插件](https://github.com/bulenkov/RedlineSmalltalk)


##实现新模块类型


通常通过实现附加到项目的某些模块类型来完成对特定工具和技术的额外支持.

新模块类型应该从类派生

[ModuleType](upsource:///platform/lang-api/src/com/intellij/openapi/module/ModuleType.java).


##项目向导


可以在包中找到配置自定义项目向导的主要实用程序

[lang-api.ide.util.projectWizard](upsource:///platform/lang-api/src/com/intellij/ide/util/projectWizard).

这些类和接口用于以下目的:


*修改配置向导视图

*向向导添加新步骤

*为项目创建提供额外设置

*项目创建期间的处理活动

*初始环境配置


###模块类型


创建新模块类型和扩展名


```xml
<moduleType id="MY_MODULE" implementationClass="st.redline.smalltalk.module.MyModuleType"/>
```
to the
[plugin.xml](https://github.com/bulenkov/RedlineSmalltalk/blob/master/resources/META-INF/plugin.xml).
A custom module type should extend the
[ModuleType](upsource:///platform/lang-api/src/com/intellij/openapi/module/ModuleType.java)
generic from
[ModuleBuilder](upsource:///platform/lang-api/src/com/intellij/ide/util/projectWizard/ModuleBuilder.java).
The following
[module type implementation](https://github.com/bulenkov/RedlineSmalltalk/blob/master/src/st/redline/smalltalk/module/RsModuleType.java)
of a custom module type show how this instance can be registered and implemented.

###实现Module Builder


设置新模块环境

[ModuleBuilder](upsource:///platform/lang-api/src/com/intellij/ide/util/projectWizard/ModuleBuilder.java)

class应该被扩展并注册为扩展点,如下面的代码段所示:


```xml
<extensions>
    <!--Place your extensions here-->
    <moduleBuilder builderClass="org.jetbrains.plugins.ruby.rails.facet.versions.MyModuleBuilder"/>
</extensions>
```

强制实施的功能包括:


*通过覆盖为新模块设置根模型


   ```java
   public abstract void setupRootModel(ModifiableRootModel modifiableRootModel) throws ConfigurationException;
   ```

*获取模块类型


   ```java
   public abstract ModuleType getModuleType();
   ```

看到

[JavaModuleBuilder](upsource:///java/openapi/src/com/intellij/ide/util/projectWizard/JavaModuleBuilder.java)

更好地了解如何实现模块构建器.


如果您的模块类型基于java模块并且也意味着支持Java,那么扩展

[JavaModuleBuilder](upsource:///java/openapi/src/com/intellij/ide/util/projectWizard/JavaModuleBuilder.java)

足够.

不需要注册扩展点.

参考

[SmallTalk模块类型](https://github.com/bulenkov/RedlineSmalltalk/blob/master/src/st/redline/smalltalk/module/RsModuleType.java)

看怎么样

[JavaModuleBuilder](upsource:///java/openapi/src/com/intellij/ide/util/projectWizard/JavaModuleBuilder.java)

可以派生出来.


###实现Module Builder Listener


模块构建器侦听器对新模块创建做出反应,这可以作为项目创建过程的一部分来完成,

或者将新模块添加到现有项目中.

要在创建模块后立即提供某种行为,模块构建器应该实现

[ModuleBuilderListener](upsource:///platform/lang-api/src/com/intellij/ide/util/projectWizard/ModuleBuilderListener.java)

方法


```java
public void moduleCreated(@NotNull final Module module);
```
executed tasks right after a module has been created,
these may include configuring roots looking up for an SDK and setting it up, adding a specific facet if required and others.
For more details please see the following
[SmallTalk custom module type](https://github.com/bulenkov/RedlineSmalltalk/blob/master/src/st/redline/smalltalk/module/RsModuleType.java)
implementation.

###添加新向导步骤


可以通过覆盖模块向导来添加新步骤


```java
public ModuleWizardStep[] createWizardSteps(WizardContext wizardContext, ModulesProvider modulesProvider);
```
method in a custom
[module builder](https://github.com/bulenkov/RedlineSmalltalk/blob/master/src/st/redline/smalltalk/module/RsModuleBuilder.java).
If this method returns a non-empty array of ModuleWizardStep objects, new steps will be shown in their indexing order while creating a new module.
The following
[implementation](https://github.com/bulenkov/RedlineSmalltalk/blob/master/src/st/redline/smalltalk/module/RsModuleWizardStep.java)
for the SmallTalk project type illustrates how a custom wizard step can be created.
The
[RsModuleWizardStep](https://github.com/bulenkov/RedlineSmalltalk/blob/master/src/st/redline/smalltalk/module/RsModuleWizardStep.java)
class is derived from
[ModuleWizardStep](upsource:///platform/lang-api/src/com/intellij/ide/util/projectWizard/ModuleWizardStep.java),
which has two methods to be overridden:

*  ```java
   public JComponent getComponent();
   ```
   defines how the step will look like
*  ```java
   public void updateDataModel();
   ```
   commits data from UI into ModuleBuilder and WizardContext

##方面


IntelliJ中的构面是存储多种特定于模块的设置的方式,例如,在某些给定模块中提供语言支持或框架.

要从最终用户的角度更好地理解方面,请参阅

[刻面](/reference_guide/project_model/facet.md)

文档部分.


##实现项目结构检测器


要在从现有源导入项目时支持模块的创建,请扩展[ProjectStructureDetector](upsource:///java/idea-ui/src/com/intellij/ide/util/projectWizard/importSources/ProjectStructureDetector.java) 
.


要检测文件,模块支持实现


```java
public abstract DirectoryProcessingResult detectRoots(@NotNull File dir, @NotNull File[] children, @NotNull File base,
                                                        @NotNull List<DetectedProjectRoot> result);
```

请参阅[Smalltalk项目结构检测器](https://github.com/bulenkov/RedlineSmalltalk/blob/master/src/st/redline/smalltalk/module/RsProjectStructureDetector.java)


但是检测文件是不够的,你还需要通过实现`setupProjectStructure()`来为项目创建一个模块.
以下是在项目结构中不存在其他模块时创建模块的示例.


```java
@Override
    public void setupProjectStructure(@NotNull final Collection<DetectedProjectRoot> roots,
        @NotNull final ProjectDescriptor projectDescriptor,
        @NotNull final ProjectFromSourcesBuilder builder)
    {
        List<ModuleDescriptor> modules = projectDescriptor.getModules();
        if (modules.isEmpty())
        {
            modules = new ArrayList<>();
            for (DetectedProjectRoot root : roots)
            {
                modules.add(
                    new ModuleDescriptor(root.getDirectory(), MyModuleType.getInstance(),
                        ContainerUtil.emptyList()));
            }
            projectDescriptor.setModules(modules);
        }
    }
```


