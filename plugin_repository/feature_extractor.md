---
title: Plugin Recommendations
---

IntelliJ平台IDE将推荐一个插件,以便在以下某种情况下进行安装:


*如果项目包含运行的IDE不支持但受插件支持的类型的文件
   
在插件存储库中可用;
 
*如果项目是在安装了插件的其他IDE安装中创建的,并且包含共享
   
使用插件配置的设置(模块,构面,工件或运行配置).


IntelliJ Platform IDE仅在适当的上下文中显示插件建议,因此它们对用户有意义.


[`feature extractor`](https://github.com/JetBrains/intellij-plugin-verifier/tree/master/intellij-feature-extractor/)工具

为插件编目这些类型的功能.
它通过静态分析插件的字节码来提取值

传递给IntelliJ Platform API,支持扩展上面列表中的功能.

但是,如果在插件中动态评估值,则“特征提取器”可能会返回不完整的结果.

如果您在[功能列表](https://plugins.jetbrains.com/feature/)中找不到您的插件,您可以编写代码

更容易进行分析,或要求JetBrains手动添加任何遗漏的要素类型.


## 文件类型


插件可以支持特定的文件类型(文件扩展名).
当在IDE中打开具有特定扩展名的文件时,将向用户显示提示,提示他们安装插件.

你应该扩展[FileTypeFactory](upsource:///platform/platform-api/src/com/intellij/openapi/fileTypes/FileTypeFactory.java)

并在`createFileTypes(FileTypeConsumer)`中提供支持的文件扩展名. 
`FileTypeConsumer`的值由`feature extractor`分析.


建议安装支持_ \ * .d_扩展类型的插件:


![文件扩展名功能](img/feature_extractor_extensions.png)


请参阅[注册文件类型](/reference_guide/custom_language_support/registering_file_type.md)以在插件中提供此功能.


##运行配置类型


如果希望IDE显示您的插件支持“运行配置类型”,则需要实现

[ConfigurationType](upsource:///platform/lang-api/src/com/intellij/execution/configurations/ConfigurationType.java)

并实现`getId()`方法. 
`feature extractor`分析`getId()`的值.


建议安装支持* Run D App *配置类型的插件:


![配置功能类型](img/feature_extractor_configuration.png)


请参阅[运行配置](/basics/run_configurations.md)以获取有关如何在插件中声明此功能的更多信息.


## Facet Type


要支持Facet Type功能,您应该扩展[FacetType](upsource:///platform/lang-api/src/com/intellij/facet/FacetType.java),

并将`stringId`传递给它的构造函数. 
`stringId`参数的值将由`feature extractor'分析.


安装支持* jangaroo * Facet类型的插件的通知:


![Facet Type of Feature](img/feature_extractor_facet.png)


有关其他信息,请参阅[Facet](/reference_guide/project_model/facet.md).


##模块类型


如果您希望IDE显示您的插件可以支持创建特定模块类型的提示,则应该扩展

[ModuleType](upsource:///platform/lang-api/src/com/intellij/openapi/module/ModuleType.java)

并将Module Type的`id`参数传递给它的构造函数. 
`feature extractor`将评估`id`的值.


参见[Module](/reference_guide/project_model/module.md)和[Supporting Module Types](/tutorials/project_wizard/module_types.md)

有关支持模块类型的更多信息.


##神器类型


要支持特定的工件类型,请扩展[ArtifactType](upsource:///java/compiler/openapi/src/com/intellij/packaging/artifacts/ArtifactType.java),

并将`id`参数传递给它的构造函数. 
`id`参数的值由`feature extractor'分析.


启用支持* dm.bundle *插件类型的插件的示例建议:


![Artifact Type of Feature](img/feature_extractor_artifacts.png)


