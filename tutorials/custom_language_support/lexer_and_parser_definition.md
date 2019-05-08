---
title: 4. Lexer and Parser Definition
---

词法分析器定义文件内容如何分解为令牌。

创建词法分析器的最简单方法是使用[JFlex](https://jflex.de/)


### 4.1。
定义词法分析器


使用我们词法分析器的规则定义*/com/simpleplugin/Simple.flex *文件。


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/Simple.flex %}
```

### 4.2。
生成词法分类器


现在我们可以从`Simple.flex`文件的上下文菜单中通过* JFlex Generator *生成一个词法分析器类。

Grammar-Kit插件使用JFlex lexer生成。

如果您是第一次运行它，它会让您选择一个文件夹来下载JFlex库和骨架。

选择项目根目录。


之后，IDE生成词法分析器:* com.simpleplugin.SimpleLexer *。


### 4.3。
定义适配器


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleLexerAdapter.java %}
```

### 4.4。
定义根文件


在`com.simpleplugin.psi`命名空间中创建类。


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/psi/SimpleFile.java %}
```

### 4.5。
定义解析器定义


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleParserDefinition.java %}
```

### 4.6。
注册解析器定义


```xml
<lang.parserDefinition language="Simple" implementationClass="com.simpleplugin.SimpleParserDefinition"/>
```

### 4.7。
运行该项目


使用以下内容创建属性文件:


```
# You are reading the ".properties" entry.
! The exclamation mark can also mark text as comments.
website = http://en.wikipedia.org/
language = English
# The backslash below tells the application to continue reading
# the value onto the next line.
message = Welcome to \
          Wikipedia!
# Add spaces to the key
key\ with\ spaces = This is the value that could be looked up with the key "key with spaces".
# Unicode
tab : \u0009
```

现在打开* PsiViewer *工具窗口，检查词法分析器如何将文件内容分解为标记，解析器将标记解析为PSI元素。


！[PSI Elements](img/psi_elements.png)


