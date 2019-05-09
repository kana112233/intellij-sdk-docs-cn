---
title: Registering a File Type
---

开发自定义语言插件的第一步是注册与该语言关联的文件类型.

IDE通常通过查看文件名来确定文件的类型.


自定义语言文件类型是派生自的类

[LanguageFileType](upsource:///platform/core-api/src/com/intellij/openapi/fileTypes/LanguageFileType.java),

通过了

[语言](upsource:///platform/core-api/src/com/intellij/lang/Language.java)

子类到其基类构造函数.

要注册文件类型,插件开发人员提供了一个子类

[FileTypeFactory](upsource:///platform/platform-api/src/com/intellij/openapi/fileTypes/FileTypeFactory.java),通过`com.intellij.fileTypeFactory注册

[平台扩展点](upsource:///platform/platform-resources/src/META-INF/PlatformExtensionPoints.xml).


**例**:

[LanguageFileType](upsource:///platform/core-api/src/com/intellij/openapi/fileTypes/LanguageFileType.java)

中的子类

[属性语言插件](upsource:///plugins/properties/properties-psi-api/src/com/intellij/lang/properties/PropertiesFileType.java)


要验证文件类型是否已正确注册,您可以实现

[LanguageFileType.getIcon()](upsource:///platform/core-api/src/com/intellij/openapi/fileTypes/LanguageFileType.java)

方法并验证是否为具有与您的文件类型关联的扩展名的文件显示了正确的图标.


如果您希望IDE显示一个提示,提示用户您的插件支持某些扩展,如下例所示,请查看有关[插件建议](/plugin_repository/feature_extractor.md)的信息.


![文件扩展名的功能类型](/plugin_repository/img/feature_extractor_extensions.png)


