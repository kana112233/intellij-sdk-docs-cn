---
title: Documentation
---

要提供不同类型的文档支持(工具提示在** Ctrl-hover **,快速文档弹出等),插件需要提供一个实现

[DocumentationProvider](upsource:///platform/lang-api/src/com/intellij/lang/documentation/DocumentationProvider.java)

接口并将其注册到`lang.documentationProvider`扩展点.

类中提供了此类实现的标准基类

[AbstractDocumentationProvider](upsource:///platform/lang-api/src/com/intellij/lang/documentation/AbstractDocumentationProvider.java).


**例**:

[DocumentationProvider](upsource:///plugins/properties/src/com/intellij/lang/properties/PropertiesDocumentationProvider.java)

对于

[属性语言插件](upsource:///plugins/properties/)


`getQuickNavigateInfo()`方法返回当用户将鼠标按住按下“Ctrl”的元素时要显示的文本.


