---
title: File View Providers
---

* IntelliJ IDEA * 6.0中引入了文件视图提供程序(请参阅[FileViewProvider](upsource:///platform/core-api/src/com/intellij/psi/FileViewProvider.java)).
其主要目的是管理单个文件中对多个PSI树的访问.


例如,一个JSPX页面有一个单独的PSI树,用于其中的Java代码(`PsiJavaFile`),一个单独的XML代码树(`XmlFile`),以及一个单独的JSP for JSP [JspFile](upsource) 
:///java/jsp-openapi/src/com/intellij/psi/jsp/JspFile.java)).


每个PSI树都覆盖了文件的全部内容,并且在可以找到不同语言的内容的地方包含特殊的“外部语言元素”.


一个[`FileViewProvider`](upsource:///platform/core-api/src/com/intellij/psi/FileViewProvider.java)实例对应一个`VirtualFile`,一个`Document`,可以用来
检索多个`PsiFile`实例.


##如何获得FVP？


*来自VirtualFile:`PsiManager.getInstance(project).findViewProvider()`

*来自PSI文件:`psiFile.getViewProvider()`


## FVP怎么办？


*要获取文件中存在PSI树的所有语言的集合:`fileViewProvider.getLanguages()`

*获取特定语言的PSI树:`fileViewProvider.getPsi(language)`,其中`language`参数可以取[Language]的值(upsource:///platform/core-api/src/com/
intellij/lang/Language.java)在[StdLanguages](upsource:///platform/platform-api/src/com/intellij/lang/StdLanguages.java)类中定义的类型.
例如,要获取XML的PSI树,请使用`fileViewProvider.getPsi(StdLanguages.XML)`.

*要在文件中指定的偏移量处查找特定语言的元素:`fileViewProvider.findElementAt(offset,language)`


##如何扩展FileViewProvider？


要创建具有针对不同语言的多个散布树的文件类型,您的插件必须包含* IntelliJ Platform *核心中可用的`fileType.fileViewProviderFactory` [扩展点](/basics/plugin_structure/plugin_extensions_and_extension_points.md)的扩展.


此扩展点使用[FileTypeExtensionPoint]声明(upsource:///platform/core-api/src/com/intellij/openapi/fileTypes/FileTypeExtensionPoint.java)

豆类.


要访问此扩展点,请创建一个实现[FileViewProviderFactory](upsource:///platform/core-api/src/com/intellij/psi/FileViewProviderFactory.java)接口的Java类,并在此类中覆盖` 
createFileViewProvider`方法.


要声明`fileType.fileViewProviderFactory`扩展点的扩展,请将以下语法添加到`plugin.xml`文件的`<extensions>`部分:


```xml
<extensions>
  <fileType.fileViewProviderFactory filetype="%file_type%" implementationClass="%class_name%" />
</extensions>
```

其中`％file_type％`指的是正在创建的文件的类型(例如,“JFS”),而`％class_name％`指的是实现[`FileViewProviderFactory`]的Java类的名称(upsource: 
///platform/core-api/src/com/intellij/psi/FileViewProviderFactory.java)界面.


