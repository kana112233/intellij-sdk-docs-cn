---
title: 14. Structure View Factory
---

结构视图工厂允许在* Structure *工具窗口中显示任何文件的结构，以便在项目之间轻松导航。


### 14.1。
定义结构视图工厂


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleStructureViewFactory.java %}
```

### 14.2。
定义结构视图模型


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleStructureViewModel.java %}
```

### 14.3。
定义结构视图元素


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleStructureViewElement.java %}
```

### 14.4。
注册结构视图工厂


```xml
<lang.psiStructureViewFactory language="Simple" implementationClass="com.simpleplugin.SimpleStructureViewFactory"/>
```

### 14.5。
运行该项目


![结构视图](img/structure_view.png)


