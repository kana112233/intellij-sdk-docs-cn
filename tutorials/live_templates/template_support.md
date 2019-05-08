---
title: 1. Adding Live Template Support
---

##模板创建


最初，您需要从头开始[创建新的实时模板](https://www.jetbrains.com/idea/help/creating-and-editing-live-templates.html#d1476224e158)。
添加新模板组“Markdown”并在此组下创建新的实时模板。
然后给模板一个缩写(例如“[”)和描述(例如“New markdown link”)。
将以下代码段粘贴到*模板文本*中:


```
[$TEXT$]($LINK$)$END$
```

可以在* Edit variables *对话框中进一步配置变量`$ TEXT $`和`$ LINK $`，以重新排序它们的优先级并绑定到将在适当的时间调用自动完成的函数，以及许多其他[有用的函数] 
](https://www.jetbrains.com/idea/help/creating-and-editing-template-variables.html)。
在实现插件中的任何特殊功能之前，开发人员应熟悉所提供的功能，以防所需功能作为[预定义功能]提供(https://www.jetbrains.com/idea/help/creating-and-editing 
-template-variables.html＃predefined_functions)。


最后，为您的新实时模板提供适用的上下文(即“Everywhere”或“Other”)。


##导出实时模板


Once confident the Live Template produces the expected result (consider testing it inside the current editor to minimize debugging later), export the Live Template (**File \| Export Settings \| ☑ Live Templates**). Unpack the resulting archive, and inside a directory `./templates/` there will be a file  called `Markdown.xml` with the following contents:

```xml
<templateSet group="Markdown">
  <template name="[" value="[$TEXT$]($LINK$)$END$" description="New link reference." toReformat="false" toShortenFQNames="false">
    <variable name="TEXT" expression="" defaultValue="" alwaysStopAt="true" />
    <variable name="LINK" expression="complete()" defaultValue="" alwaysStopAt="true" />
    <context>
      <option name="OTHER" value="true" />
    </context>
  </template>
</templateSet>
```

将此文件复制到插件的资源中(例如`project/resource/liveTemplates/Markdown.xml`。


##实现DefaultLiveTemplatesProvider


[`DefaultLiveTemplatesProvider`](upsource:///platform/lang-impl/src/com/intellij/codeInsight/template/impl/DefaultLiveTemplatesProvider.java)告诉我们在哪里可以找到实时模板设置文件。
确保包含相对于资源目录的文件的完整路径，不包括文件名。


```java
{% include /code_samples/live_templates/src/MarkdownTemplateProvider.java %}
```

##实现TemplateContextType


A [`TemplateContextType`](upsource:///platform/lang-api/src/com/intellij/codeInsight/template/TemplateContextType.java)告诉我们实时模板的适用位置。


```java
{% include /code_samples/live_templates/src/MarkdownContext.java %}
```

定义`TemplateContextType`后，请务必将分配的上下文类型添加到先前创建的实时模板设置文件中。
在`<template> ... </template>`下添加以下上下文:


```xml
<context>
  <option name="MARKDOWN" value=true />
</context>
```

并不总是需要定义自己的`TemplateContextType`，因为IntelliJ平台中已经定义了许多现有的模板上下文。
如果要增加对现有区域的语言支持，请考虑重复使用[许多现有模板上下文]之一(upsource:///platform/lang-api/src/com/intellij/codeInsight/template/TemplateContextType.java)。


##注册扩展点


```xml
{% include /code_samples/live_templates/resources/META-INF/plugin.xml %}
```

##检查插件


Now check that the plugin is working correctly. Run the plugin and verify there is a new entry under *File \| Settings \| Live Templates \| Markdown \| \[*. Finally, create a new file `Test.md` and confirm that the Live Template works.


