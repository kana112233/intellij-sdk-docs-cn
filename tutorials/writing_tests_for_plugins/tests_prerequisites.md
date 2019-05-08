---
title: 1. Tests Prerequisites
---

### 1.1。
为测试创建一个文件夹


使用插件打开项目并创建一个单独的文件夹“tests”。

通过上下文菜单“Mark Directory As”＆rarr将文件夹标记为测试源根目录; 
`测试源根。


### 1.2。
为测试数据创建一个文件夹


在我们的测试中，我们将使用测试数据，因此我们需要一个文件夹*“testData”*来存储这些文件。


### 1.3。
运行配置参数


由于我们的一些测试将使用Java文件作为测试数据，因此我们需要模拟项目SDK。

当我们使用特殊实用程序类时，IntelliJ IDEA会自动执行所有操作

[LightCodeInsightFixtureTestCase](upsource:///java/testFramework/src/com/intellij/testFramework/fixtures/LightCodeInsightFixtureTestCase.java)。


我们需要做的就是将运行配置工作目录指向IntelliJ IDEA Community Edition源的根目录，并启用以下VM选项:


```
-ea -Xbootclasspath/p:../out/classes/production/boot -XX:+HeapDumpOnOutOfMemoryError -Xmx512m -XX:MaxPermSize=320m -Didea.system.path=../test-system -Didea.home.path=../ -Didea.config.path=../test-config -Didea.test.group=ALL_EXCLUDE_DEFINED
```

**注意**:


>请记住，我们已经更改了_working目录_，因此测试中的所有路径都从中扩展而来
  
> [LightCodeInsightFixtureTestCase](upsource:///java/testFramework/src/com/intellij/testFramework/fixtures/LightCodeInsightFixtureTestCase.java)
  
>将使用IntelliJ IDEA Community Edition的_source root_的相对路径。


