---
title: 3. Completion Test
---

在此测试中，我们将检查[自定义语言支持教程](/tutorials/custom_language_support_tutorial.md)的[Reference Contributor](/tutorials/custom_language_support/reference_contributor.md)部分中实现的代码完成是否正常工作。


### 3.1。
定义测试数据


创建一个文件* DefaultTestData.simple *。


```bash
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

再创建一个文件* CompleteTestData.java *。


```java
{% include /code_samples/simple_language_plugin/testData/CompleteTestData.java %}
```

### 3.2。
定义一个测试


```java
{% include /code_samples/simple_language_plugin/tests/com/simpleplugin/SimpleCodeInsightTest.java %}
```

### 3.3。
运行测试


运行测试并确保它是绿色的。


