---
title: 5. Syntax Highlighter and Color Settings Page
---

### 5.1。
定义语法高亮显示器


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleSyntaxHighlighter.java %}
```

### 5.2。
定义语法高亮显示器工厂


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleSyntaxHighlighterFactory.java %}
```

### 5.3。
注册语法高亮显示器工厂


```xml
<lang.syntaxHighlighterFactory language="Simple" implementationClass="com.simpleplugin.SimpleSyntaxHighlighterFactory"/>
```

### 5.4。
运行该项目


![语法荧光笔](img/syntax_highlighter.png)


### 5.5。
定义颜色设置页面


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleColorSettingsPage.java %}
```

### 5.6。
注册颜色设置页面


```xml
<colorSettingsPage implementation="com.simpleplugin.SimpleColorSettingsPage"/>
```

### 5.7。
运行该项目


![颜色设置页面](img/color_settings_page.png)


