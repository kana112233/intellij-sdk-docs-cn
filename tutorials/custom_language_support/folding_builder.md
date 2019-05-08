---
title: 12. Folding Builder
---

折叠构建器可帮助您折叠代码区域并将其替换为特定文本。


### 12.1。
定义折叠构建器


我们默认使用其值替换属性的用法。


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleFoldingBuilder.java %}
```

### 12.2。
注册折叠构建器


```xml
<lang.foldingBuilder language="JAVA" implementationClass="com.simpleplugin.SimpleFoldingBuilder"/>
```

### 12.3。
运行该项目


现在，当我们打开一个Java文件时，它会显示属性的值而不是键。


！[折叠](IMG/folding.png)


