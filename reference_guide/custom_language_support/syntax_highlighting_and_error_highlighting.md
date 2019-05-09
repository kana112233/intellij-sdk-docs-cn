---
title: Syntax Highlighting and Error Highlighting
---

用于指定应如何突出显示特定范围的文本的类被调用

[TextAttributesKey](upsource:///platform/core-api/src/com/intellij/openapi/editor/colors/TextAttributesKey.java).

为每个应突出显示的不同类型的项目(关键字,数字,字符串等)创建此类的实例.

该

[TextAttributesKey](upsource:///platform/core-api/src/com/intellij/openapi/editor/colors/TextAttributesKey.java)

定义应用于相应类型的项的默认属性(例如,关键字为粗体,数字为蓝色,字符串为粗体和绿色).

的映射

[TextAttributesKey](upsource:///platform/core-api/src/com/intellij/openapi/editor/colors/TextAttributesKey.java)

编辑器中使用的特定属性由

[EditorColorsScheme](upsource:///platform/editor-ui-api/src/com/intellij/openapi/editor/colors/EditorColorsScheme.java)

如果插件提供了适当的配置界面,则可以由用户配置.

从多个突出显示

[TextAttributesKey](upsource:///platform/core-api/src/com/intellij/openapi/editor/colors/TextAttributesKey.java)

项可以分层 - 例如,一个键可以定义项的粗体,另一个键可以定义其颜色.


**注意:**

关于语言默认值的新功能以及对其他颜色方案的支持,详见

[配色方案管理](/reference_guide/color_scheme_management.md).


### Lexer


语法和错误突出显示在多个级别上执行.

第一级语法突出显示基于词法分析器输出,并通过

[SyntaxHighlighter的](upsource:///platform/editor-ui-api/src/com/intellij/openapi/fileTypes/SyntaxHighlighter.java)

接口.

语法高亮显示返回

[TextAttributesKey](upsource:///platform/core-api/src/com/intellij/openapi/editor/colors/TextAttributesKey.java)

每种令牌类型的实例需要特殊突出显示.

用于突出显示词法错误,标准

[TextAttributesKey](upsource:///platform/core-api/src/com/intellij/openapi/editor/colors/TextAttributesKey.java)

对于坏人物

[HighlighterColors.BAD_CHARACTER](upsource:///platform/editor-ui-api/src/com/intellij/openapi/editor/HighlighterColors.java)

可以使用.


**例:**

[SyntaxHighlighter的](upsource:///plugins/properties/properties-psi-api/src/com/intellij/lang/properties/PropertiesHighlighter.java)

实施

[属性语言插件](upsource:///plugins/properties/)


### Parser


在解析期间发生第二级错误突出显示.

如果特定的令牌序列根据语言的语法无效,则

[PsiBuilder.error()](upsource:///platform/core-api/src/com/intellij/lang/PsiBuilder.java)

方法可用于突出显示无效令牌并显示错误消息,说明它们无效的原因.


### Annotator


第三级突出显示是通过

[注释器(upsource:///platform/analysis-api/src/com/intellij/lang/annotation/Annotator.java)

接口.

插件可以在`com.intellij.annotator`扩展点中注册一个或多个注释器,并且在后台突出显示传递期间调用这些注释器以处理自定义语言的PSI树中的元素.

注释器不仅可以使用PSI分析语法,还可以分析语义,因此可以提供更复杂的语法和错误突出显示逻辑.

注释器还可以为其检测到的问题提供快速修复.


更改文件时,将逐步调用注释器以仅处理PSI树中已更改的元素.


要将文本区域突出显示为警告或错误,注释器会调用“createErrorAnnotation()”或“createWarningAnnotation()”.

[AnnotationHolder](upsource:///platform/analysis-api/src/com/intellij/lang/annotation/AnnotationHolder.java)

对象传递给它,并可选择在返回的上调用`registerFix()`

[注释](upsource:///platform/analysis-api/src/com/intellij/lang/annotation/Annotation.java)

对象为错误或警告添加快速修复.

要应用其他语法突出显示,注释器可以调用

[AnnotationHolder.createInfoAnnotation()](upsource:///platform/analysis-api/src/com/intellij/lang/annotation/AnnotationHolder.java)

用空消息然后调用

[Annotation.setTextAttributes()](upsource:///platform/analysis-api/src/com/intellij/lang/annotation/Annotation.java)

指定突出显示的文本属性键.


**例:**

[注释器(upsource:///plugins/properties/properties-psi-impl/src/com/intellij/lang/properties/PropertiesAnnotator.java)

对于

[属性语言插件](upsource:///plugins/properties/)


###外部工具


最后,如果自定义语言使用外部工具来验证语言中的文件(例如,使用Xerces库进行XML模式验证),它可以提供实现

[ExternalAnnotator](upsource:///platform/analysis-api/src/com/intellij/lang/annotation/ExternalAnnotator.java)

接口并将其注册到`com.intellij.externalAnnotator`扩展点.

该

[ExternalAnnotator](upsource:///platform/analysis-api/src/com/intellij/lang/annotation/ExternalAnnotator.java)

突出显示具有最低优先级,仅在所有其他后台处理完成后才会调用.

它使用相同的

[AnnotationHolder](upsource:///platform/analysis-api/src/com/intellij/lang/annotation/AnnotationHolder.java)

用于将外部工具的输出转换为编辑器突出显示的界面.


###颜色设置


该插件还可以提供配置界面,以允许用户配置用于突出显示特定项目的颜色.

为了做到这一点,它应该提供一个实现

[ColorSettingPage](upsource:///platform/lang-api/src/com/intellij/openapi/options/colors/ColorSettingsPage.java)

并将其注册到`com.intellij.colorSettingsPage`扩展点.


**例**:

[ColorSettingsPage](upsource:///plugins/properties/src/com/intellij/openapi/options/colors/pages/PropertiesColorsPage.java)

对于

[属性语言插件](upsource:///plugins/properties/)


“导出到HTML”功能使用与编辑器相同的语法突出显示机制,因此它将自动为提供语法高亮显示的自定义语言工作.


