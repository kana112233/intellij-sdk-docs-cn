---
title: 9. Completion Contributor
---

提供完成的最简单方法是使用完成贡献者。


### 9.1。
定义完成贡献者


让我们为属性文件中的值提供自定义完成。


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleCompletionContributor.java %}
```

### 9.2。
注册完成贡献者


```xml
<completion.contributor language="Simple" implementationClass="com.simpleplugin.SimpleCompletionContributor"/>
```

### 9.3。
运行该项目


![完成](IMG/completion.png)


