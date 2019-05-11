---
title: 16. Code Style Setting
---

代码样式设置允许定义格式选项。
代码样式设置提供程序将创建设置的实例，还可以创建选项页面以进行编辑。
在此示例中，我们将创建一个页面，该页面使用由语言代码样式设置提供程序自定义的默认语言代码样式设置。


### 16.1。
定义代码样式设置


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleCodeStyleSettings.java %}
```

### 16.2。
定义代码样式设置提供程序


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleCodeStyleSettingsProvider.java %}
```

### 16.3。
注册代码样式设置提供程序


```xml
<codeStyleSettingsProvider implementation="com.simpleplugin.SimpleCodeStyleSettingsProvider"/>
```

### 16.4。
定义语言代码样式设置提供者


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleLanguageCodeStyleSettingsProvider.java %}
```

### 16.5。
注册语言代码样式设置提供程序


```xml
<langCodeStyleSettingsProvider implementation="com.simpleplugin.SimpleLanguageCodeStyleSettingsProvider"/>
```

### 16.6。
运行该项目


![代码样式设置](img/code_style_settings.png)


