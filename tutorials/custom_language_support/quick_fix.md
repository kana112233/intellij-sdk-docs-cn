---
title: 18. Quick Fix
---

快速修复允许通过**显示意图操作**(Alt + Enter)对代码应用自动更改。


让我们添加一个快速修复，它有助于根据其用法定义未解析的属性。


### 18.1。
更新元素工厂


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/psi/SimpleElementFactory.java %}
```

### 18.2。
定义意图行动


快速修复将在用户选择的文件中创建属性，并在创建后导航到此属性。


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/CreatePropertyQuickFix.java %}
```

### 18.3。
更新注释器


注意对`registerFix`的调用。


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleAnnotator.java %}
```

### 18.4。
运行该项目


现在让我们尝试使用尚未定义的属性。


！[快速修复](img/quick_fix.png)


