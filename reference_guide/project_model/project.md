---
title: Project
---

在 *IntelliJ Platform* 中,项目将所有源代码,库,构建指令封装到单个组织单元中.
您在IDE中执行的所有操作都是在项目的上下文中完成的.
项目定义了一些称为模块和库的集合.
根据项目的逻辑和功能要求,您可以创建单模块或多模块项目.


##使用项目


 *IntelliJ Platform* 将项目配置数据存储在XML文件中.
这些文件的列表取决于插件[project](https://www.jetbrains.com/help/idea/about-projects.html)格式.


对于基于文件的格式项目,项目本身的信息核心(例如组件模块的位置,编译器设置等)存储在`％project_name％.ipr`文件中.
有关项目包含的模块的信息存储在`％module_name％.iml`文件中.
为每个模块创建模块文件.


对于基于目录的格式项目,项目和工作区设置存储在`％project_home_directory％/.idea`目录下的许多XML文件中.
每个XML文件都负责其自己的一组设置,并且可以通过其名称识别:`projectCodeStyle.xml`,`encodings.xml`,`vcs.xml`等.至于基于文件的格式项目,`.iml 
`文件描述模块.


请注意,您无需直接访问项目文件即可加载或保存设置.
有关详细信息,请参阅[持久性组件状态](../../basics/persisting_state_of_components.md).


要使用项目和项目文件,可以使用以下类和接口:


* [`Project`](upsource:///platform/core-api/src/com/intellij/openapi/project/Project.java)界面.

* [`ProjectRootManager`](upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/ProjectRootManager.java)抽象类.

* [`ProjectManager`](upsource:///platform/projectModel-api/src/com/intellij/openapi/project/ProjectManager.java)抽象类.

* [`ProjectFileIndex`](upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/ProjectFileIndex.java)界面.


其他用于处理项目模型的类位于[projectModel-api.openapi](upsource:///platform/projectModel-api/src/com/intellij/openapi)包中. 
[project]概念的基本API类和接口(upsource:///platform/core-api/src/com/intellij/openapi/project/Project.java),[module](upsource:///platform/core-api/src/com/intellij/openapi/module/Module.java),[application](upsource:///platform/core-api/src/com/intellij/openapi/application/Application.java)和[
组件](upsource:///platform/core-api/src/com/intellij/openapi/components/ProjectComponent.java)放在[core-api.openapi]中(upsource:///platform/core-api/src/com/intellij/openapi)包.


###如何获取项目中所有模块的源根列表？


使用`ProjectRootManager.getContentSourceRoots()`方法.
为了澄清这一点,请考虑以下代码段:


```java
String projectName = project.getName();
VirtualFile[] vFiles = ProjectRootManager.getInstance(project).getContentSourceRoots();
String sourceRootsList = Arrays.stream(vFiles).map(VirtualFile::getUrl).collect(Collectors.joining("\n"));

Messages.showInfoMessage("Source roots for the " + projectName + " plugin:\n" + sourceRootsList, "Project Properties");
```

###检查文件是否属于项目


Use [ProjectFileIndex.java](upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/ProjectFileIndex.java) to get this information:
```java
ProjectFileIndex projectFileIndex = ProjectRootManager.getInstance(project).getFileIndex();
```

###如何获取指定文件或目录所属的内容或源根目录？


使用`ProjectFileIndex.getContentRootForFile`和`ProjectFileIndex.getSourceRootForFile`方法.
例如:


```java
VirtualFile moduleContentRoot = ProjectRootManager.getInstance(project).getFileIndex().getContentRootForFile(virtualFileOrDirectory);
VirtualFile moduleSourceRoot = ProjectRootManager.getInstance(project).getFileIndex().getSourceRootForFile(virtualFileOrDirectory);
```

请注意,如果文件或目录不属于项目中任何模块的源根,则此方法返回“null”.
 

###如何检查文件或目录是否与项目库相关？


`ProjectFileIndex`接口实现了许多方法,可用于检查指定的文件是属于项目库类还是库源.


您可以使用以下方法:


*`ProjectFileIndex.**`isLibraryClassFile` **`(virtualFile)`:如果指定的`virtualFile`是编译的类文件,则返回`true`.

*`ProjectFileIndex.**`isInLibraryClasses` **`(virtualFileorDirectory)`:如果指定的`virtualFileorDirectory`属于库类,则返回`true`.

*`ProjectFileIndex.**`isInLibrarySource` **`(virtualFileorDirectory)`:如果指定的`virtualFileorDirectory`属于库源,则返回`true`.


###如何获得项目SDK？


*获取项目级SDK:`Sdk projectSdk = ProjectRootManager.getInstance(project).getProjectSdk();`

*获取项目级SDK名称:`String projectSdkName = ProjectRootManager.getInstance(project).getProjectSdkName();`


###如何设置项目SDK？


*设置项目级SDK:`ProjectRootManager.getInstance(project).setProjectSdk(Sdk jdk);`

*设置项目级SDK名称:`ProjectRootManager.getInstance(project).setProjectSdkName(String name);`


请注意,默认情况下,项目模块使用项目SDK. 
(可选)您可以为每个模块配置单独的SDK.


##更改项目结构


可以在包[projectModel-impl.openapi](upsource:///platform/projectModel-impl/src/com/intellij/openapi)中找到可用于修改项目结构的实用程序类.
它的[根](upsource:///platform/projectModel-impl/src/com/intellij/openapi/roots/)子包包含用于处理项目和模块源根的实例和实用程序,包括[ModuleRootModificationUtil.java](upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/ModuleRootModificationUtil.java)和[ProjectRootUtil.java](upsource:///platform/projectModel-impl/src/com/intellij/openapi/projectRoots/IMPL/ProjectRootUtil.java).
项目结构

需要在[write action]中执行更改(/basics/architectural_overview/general_threading_rules.md #readwrite-lock).


请参阅上面的[基本示例](https://github.com/JetBrains/intellij-sdk-docs/blob/master/code_samples/project_model/src/com/intellij/tutorials/project/model/ModificationAction.java)
 - 飞行项目结构修改,以了解如何实施.


##接收有关项目结构更改的通知


要接收有关项目结构更改的通知(要添加或删除的模块或库,更改模块依赖项等),

使用[消息总线](/reference_guide/messaging_infrastructure.md)和`ProjectTopics.PROJECT_ROOTS`主题:


```java
project.getMessageBus().connect().subscribe(ProjectTopics.PROJECT_ROOTS, new ModuleRootListener() {
  @Override
  public void rootsChanged(ModuleRootEvent event) {
  }
});
```

该事件只会通知您某些事情已发生变化;
如果你需要详细了解发生了什么变化,你

需要保留与您相关的项目结构模型的状态副本,并与之进行比较

改变后的当前状态.


