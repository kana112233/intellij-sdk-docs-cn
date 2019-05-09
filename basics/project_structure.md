---
title: Project Structure
---

<! -  TODO根据目录分成部分 - >


本主题考虑基于* IntelliJ Platform *和相关主题的项目概念,例如_modules_,_facets_,_libraries_,_SDK_.
已经考虑了可用于管理项目和模块的项目结构和Java类.


##项目及其组件


本节简要讨论IDEA项目结构,项目组件和相关术语.
有关项目及其组件的更多信息,请参阅[项目](https://www.jetbrains.com/help/idea/about-projects.html),[模块](https://www.jetbrains.com/help/idea/configurations-projects.html#working-with-modules),[Library](https://www.jetbrains.com/help/idea/working-with-libraries.html),[Facet](https://www.jetbrains.com/help/idea/adding-support-for-frameworks-and-technologies.html#facets)在* IntelliJ IDEA * Web帮助中.


###项目


在* IntelliJ Platform *中,_project_将所有源代码,库和构建指令封装到单个组织单元中.
使用* IntelliJ Platform SDK *所做的一切都是在项目的上下文中完成的.
项目定义称为_modules_和_libraries_的集合.
根据项目的逻辑和功能要求,您可以创建_single-module_或_multi-module_项目.


###模块


_module_是一个独立的功能单元,可以独立运行,测试和调试.
模块包括源代码,构建脚本,单元测试,部署描述符等.在项目中,每个模块可以使用特定的SDK或继承项目级别定义的SDK(参见[SDK](/reference_guide/project_model) 
/sdk.md)本文档下面的部分).
模块可以依赖于项目的其他模块.


### 图书馆


_library_是模块所依赖的已编译代码(例如JAR文件)的存档. 
* IntelliJ Platform *支持三种类型的库:


* **模块库**:库类仅在此模块中可见,库信息记录在模块的`.iml`文件中.

* **项目库**:库类在项目中可见,库信息记录在项目的`.ipr`文件或`.idea/libraries`中.

* **全局库**:库信息记录在`〜/.IntelliJIdea/config/options`目录的`applicationLibraries.xml`文件中.
全局库与项目库类似,但对于不同的项目是可见的.


有关库的更多信息,请参阅[Library](https://www.jetbrains.com/help/idea/working-with-libraries.html).


### SDK


每个项目都使用软件开发工具包(_SDK_).
对于Java项目,SDK称为JDK(Java Development Kit).


SDK确定用于构建项目的API库.
如果您的项目是多模块,则默认情况下项目SDK对于项目中的所有模块都是通用的.


(可选)您可以为每个模块配置单独的SDK.


有关SDK的更多信息,请参阅* IntelliJ IDEA * Web帮助中的[使用SDK](https://www.jetbrains.com/help/idea/working-with-sdks.html).


### Facet


_facet_表示特定配置,特定于与模块相关联的特定框架/技术.
模块可以有多个方面.
例如. 
Spring特定配置存储在Spring facet中.


方面记录在[Facet](https://www.jetbrains.com/help/idea/adding-support-for-frameworks-and-technologies.html#facets)和[语言和框架特定指南](https://www.jetbrains.com/help/idea/language-and-framework-specific-guidelines.html).


有关每个实体的更多信息,请参阅:


 -  [项目](/reference_guide/project_model/project.md)

 -  [Module](/reference_guide/project_model/module.md)

 -  [SDK](/reference_guide/project_model/sdk.md)

 -  [图书馆](/reference_guide/project_model/facet.md)

 -  [外部系统集成](/reference_guide/frameworks_and_external_apis/external_system_integration.md)(适用于从Gradle或类似构建系统导入的项目)


