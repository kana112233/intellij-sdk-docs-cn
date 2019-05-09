---
title: Spring API
---

Spring API允许第三方插件在IntelliJ IDEA中重用,集成或扩展现有的Spring Framework支持.


使用Spring API的流行插件是[hybris集成](https://plugins.jetbrains.com/plugin/7525-hybris-integration).


要开发插件,您需要使用IntelliJ IDEA Ultimate Edition 13.1或更高版本.


###设置IntelliJ Platform SDK


> **注意**这仅适用于[Plugin DevKit](/basics/getting_started/using_dev_kit.md)项目.
对于[Gradle](/tutorials/build_system.md)项目,只需将依赖项添加到捆绑的Spring插件`com.intellij.spring`即可.


#####新SDK

请创建IntelliJ Platform SDK以包含所有必需的最低文件.

然后将`$ IDEA_HOME $/plugins/Spring/lib/spring.jar`添加到其_classpath_(_not_到你的插件模块的依赖项).


#####现有SDK

请按照以下步骤修改现有的IntelliJ Platform SDK:


*添加到_classpath_(_not_到您的插件模块的依赖项)

*`$ IDEA_HOME $/plugins/Spring/lib/spring-api.jar` _not在最近的版本中,合并到`spring.jar`_

*`$ IDEA_HOME $/plugins/Spring/lib/spring.jar`

*添加到_sourcepath_

*`$ IDEA_HOME $/lib/src/src_spring-openapi.zip`


### 一般注意事项

如果您在插件中使用其他Spring功能(例如Spring EL),请确保将所有必需的JAR添加到IntelliJ Platform SDK类路径中,以使插件的测试正常工作.


请在插件中仅使用`spring-api.jar`(提供源代码)中公开的与Spring相关的功能.
使用Spring插件本身的任何其他“内部”(实现)类(`spring.jar`)是不受支持的.


### plugin.xml

将`<depends> com.intellij.spring </depends>`添加到你的`plugin.xml`中,要求激活“Spring Support”插件.
所有可用的扩展点都在`com.intellij.spring`前缀下提供.

请注意,“Spring Support”插件本身依赖于其他一些需要在沙箱中启用的插件(请参阅启动时的通知).


##主要概念

Spring facet可以安装在模块上. 
(几乎)所有Spring功能都需要一个现有且正确设置的Spring facet.


Spring facets通常包含一个用户配置或自动提供的文件集,它们将一组与Spring相关的配置文件(XML,Code,.properties或其他配置文件)组合在一起.


文件集通常对应于运行时的实际应用程序上下文配置.
可以通过依赖于另一个文件集(可能来自另一个模块)来建模层次结构.


作为API用户,您通常更喜欢使用`SpringModel`(它建立在fileset(s)之上).


## API更新

> **注意** 2017.3:`LocalXmlModel#setActiveProfiles`和`LocalAnnotationModel#setActiveProfiles`已被弃用,将在2018.1中删除.


> **注意**从2016.2开始,bean _type_的内部表示已从`PsiClass`更改为`PsiType`,请注意弃用.


> **注意**有些核心类已在14(.1)中更改,请参阅“_Version 14(.1)_”说明,了解有关如何替换现有API调用的信息.


## 我如何能...


### Spring Setup

要检查Spring/Spring Facet等的可用性,请参阅`com.intellij.spring.model.utils.SpringCommonUtils`.


_2016.2_参见`com.intellij.spring.SpringLibraryUtil`以获取有关正在使用的Spring的确切版本的信息.


### Spring Model


#####按文件PsiElement获取Spring模型.

请参阅`SpringManager#getSpringModel(s)...`和`com.intellij.spring.model.utils.SpringModelUtils`.


#####贡献隐式模型

请参阅`com.intellij.spring.SpringModelProvider`以提供隐式文件集(例如,由特定配置文件中的另一个框架提供).


_版本15_

有关方便的基类,请参阅`com.intellij.spring.facet.SpringAutodetectedFileSet`.
请注意,Spring facet中的用户无法编辑/修改自动检测的文件集.


#####自定义隐式模型配置

_2017.1_参见`com.intellij.spring.facet.SpringFileSetEditorCustomization`来自定义演示文稿和/或为特定的自动检测文件集添加额外的设置/操作.


#####贡献隐式bean

请参阅`com.intellij.spring.model.jam.CustomComponentsDiscoverer`或`com.intellij.spring.model.SpringImplicitBeansProviderBase`以提供隐式(特定于框架)的bean(例如Spring MVC的“servletContext”).


_版本15_

`CustomComponentsDiscoverer`已分别分为`com.intellij.spring.model.custom.CustomLocalComponentsDiscoverer`和`com.intellij.spring.model.custom.CustomModuleComponentsDiscoverer`.


#####贡献自定义bean范围

_版本14_

请参阅`com.intellij.spring.model.scope.SpringCustomBeanScope`以提供自定义(例如特定于框架)bean范围.


#####获取/创建Spring配置文件

_版本14.1_

`com.intellij.spring.profiles.SpringProfilesFactory`


### 豆子


#####按名称搜索bean

`com.intellij.spring.CommonSpringModel#findBeanByName`


_Version 14_:`com.intellij.spring.model.utils.SpringModelSearchers#findBean`


#####按类型搜索bean

选择`com.intellij.spring.CommonSpringModel#findBeansByPsiClassXXX`变体之一(请注意不推荐使用的方法).


_Version 14_:`com.intellij.spring.model.utils.SpringModelSearchers#findBeans`


_Version 16_:注意'SpringModelSearchParameters.BeanClass#withInheritors(GlobalSearchScope)的弃用


#####查明是否存在具有给定名称/类型的bean

_Version 14_:`com.intellij.spring.model.utils.SpringModelSearchers#doesBeanExist`(请注意弃用的方法)


#####将bean作为基础结构bean

_Version 14_:实现`SpringInfrastructureBean`,这样的bean获取特殊图标,可以在UI的各个地方进行过滤.


### XML配置

所有对基于XML的Spring配置文件的支持都是通过[DOM-API](xml_dom_api.md)提供的.


#####添加对其他Spring命名空间的支持

请参阅EP`com.intellij.spring.dom.SpringCustomNamespaces`,注册的namespace-key必须与通过`@ Namespace`注册的DOM元素匹配.

通过标准的`DomExtender <Beans>`EP或`com.intellij.spring.dom.SpringCustomNamespaces #registerExtensions`(版本14)注册可用元素.


请注意`getModelVersion`和`getStubVersion`(参见javadoc).


#####在我的DomElement中添加对Spring Bean的引用

使用以下模板:


```java
@Convert(SpringBeanResolveConverter.class)
@RequiredBeanType("fqn.to.required.class")//optional
GenericAttributeValue<SpringBeanPointer> getMyAttributeName();
```

###代码配置


#####在我的JamElement中添加对Spring Bean的引用

_版本14_


```java
JamStringAttributeMeta.Single<SpringBeanPointer> ATTRIBUTE_META =
    JamAttributeMeta.singleString("attributeName",
        new SpringBeanReferenceJamConverter("fqn.to.required.class"));
```

##### @AliasFor

_版本16_

请参阅`com.intellij.spring.model.aliasFor.SpringAliasForUtils`以获取相应的`@ AliasFor` JAM.


### spring.factories

_版本15_

请参阅`com.intellij.spring.spi.SpringSpiManager`.


### IDE功能


##### Add inspections to Spring Validator
Add additional inspections (e.g. for custom namespace) to Spring Validator (*Settings|Compiler|Validation*) via EP `com.intellij.spring.SpringInspectionsRegistry$Contributor`.

#####将其他文件添加到Spring Validator

_版本14.1_

可以通过`com.intellij.spring.SpringInspectionsRegistry $ AdditionalFilesContributor注册通过Spring Validator注册的检查处理的其他文件(例如特定的`.properties`配置文件).


#####为其他框架配置Spring支持

当通过框架向导添加Spring facet时,使用`com.intellij.spring.facet.SpringConfigurator`提供“自动”配置.


##### UI/Presentation

请不要直接引用`SpringApiIcons`中的bean图标,而是使用`SpringPresentationProvider`重新使用统一的图标/bean名称.
有关弹出/列表渲染器的信息,请参阅`SpringBeansPsiElementCellRenderer`.


#Spring Boot

_2018.1_


Spring Boot API允许在IDE中扩展/访问Spring Boot特定的支持.


> **警告**虽然我们试图保持兼容性,但请为不太严格的政策做好准备.


### 配置

请执行以下步骤_additionally_以设置Spring API支持(请参阅[此处](#setting-up-intellij-platform-sdk)):


*添加到_classpath_(_not_到您的插件模块的依赖项)

*`$ IDEA_HOME $/plugins/SpringBoot/lib/spring-boot.jar`

*`$ IDEA_HOME $/plugins/SpringBoot/lib/spring-boot-initializr.jar`(可选)

*`$ IDEA_HOME $/plugins/SpringBoot/lib/spring-boot-run.jar`(可选)

*添加到_sourcepath_

*`$ IDEA_HOME $/lib/src/src_spring-boot-openapi.zip`


### plugin.xml

将`<depends> com.intellij.spring.boot </depends>`添加到你的`plugin.xml`中,要求激活“Spring Boot”插件.
所有可用的扩展点都在`com.intellij.spring.boot`前缀下提供.


### Spring Boot Library

使用`com.intellij.spring.boot.library.SpringBootLibraryUtil`查询常见附加库的版本和可用性.


###自定义配置文件格式

`com.intellij.spring.boot.model.SpringBootModelConfigFileContributor`允许添加对自定义配置文件格式的支持.
                                            

###自动配置支持

现有的`Condition`实现可以在IDE中通过`com.intellij.spring.boot.model.autoconfigure.conditions.ConditionalContributor`在设计时进行模拟.


实现`com.intellij.spring.boot.model.autoconfigure.conditions.jam.ConditionalOnJamElement`的自定义`@ConditionalOn ...`注释将自动添加到评估中.


### Spring Initializr

需要`spring-boot-initializr.jar`


`com.intellij.spring.boot.initializr.SpringInitializrModuleBuilderPostTask`允许在创建模块后执行自定义设置步骤(例如,与构建系统的设置集成).


###端点选项卡

_2018.2_  - 需要`spring-boot-run.jar`


使用EP`com.intellij.spring.boot.run.endpoint`添加自定义执行器端点选项卡.
任何设置都应该通过`com.intellij.spring.boot.run.endpointTabConfigurable` EP在“Spring Boot”设置选项卡中公开.


