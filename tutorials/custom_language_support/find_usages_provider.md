---
title: 11. Find Usages Provider
---

查找使用提供程序使用单词扫描程序来构建每个文件中存在的单词索引。

扫描程序将文本分解为单词，定义每个单词的上下文并将其传递给查找使用提供程序。


### 11.1。
定义查找用法提供程序


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleFindUsagesProvider.java %}
```

### 11.2。
注册查找用法提供程序


```xml
<lang.findUsagesProvider language="Simple" implementationClass="com.simpleplugin.SimpleFindUsagesProvider"/>
```

### 11.3。
运行该项目


现在我们可以通过参考调用任何属性的* Find Usages *。


！[查找用法](img/find_usages.png)


