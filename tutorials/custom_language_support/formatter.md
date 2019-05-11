---
title: 15. Formatter
---

*格式化程序允许根据代码样式设置自动重新格式化代码。*


### 15.1。
定义一个块


格式化程序使用块来接收每个PSI元素的格式规则。

我们的目标是用这样的块覆盖每个PSI元素。
由于每个块都构建自己的子块，我们可以生成额外的块或跳过任何PSI元素。


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleBlock.java %}
```

### 15.2。
定义格式模型构建器


让我们定义一个格式化程序，删除除属性分隔符周围的单个空格之外的额外空格。


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleFormattingModelBuilder.java %}
```

### 15.3。
注册格式化程序


```xml
<lang.formatter language="Simple" implementationClass="com.simpleplugin.SimpleFormattingModelBuilder"/>
```

### 15.4。
运行该项目


Now add some extra spaces and reformat the code via **Code \| Reformat Code**.

![格式化](IMG/formatter.png)


