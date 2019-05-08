---
title: 17. Commenter
---

评论者允许用户通过相应的操作自动注释光标处的代码或选定的代码。


### 17.1。
定义评论者


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleCommenter.java %}
```

### 17.2。
注册评论者


```xml
<lang.commenter language="Simple" implementationClass="com.simpleplugin.SimpleCommenter"/>
```

### 17.3。
运行该项目


！[注释器(IMG/commenter.png)


