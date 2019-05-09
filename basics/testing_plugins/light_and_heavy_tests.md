---
title: Light and Heavy Tests
---

插件测试在真实的,而不是模拟的* IntelliJ Platform *环境中运行,并使用大多数应用程序和项目组件的实际实现.


加载项目的所有项目组件以运行测试是一项相当昂贵的操作,我们希望避免为每个测试执行此操作.
根据加载和执行时间,我们在* IntelliJ Platform *测试框架中提供* heavy * tests和* light *测试之间的区别:
 

* *重*测试为每个测试创建一个新项目.

* * Light *尽可能重用之前测试运行的项目.


轻型和重型测试使用不同的基类或夹具类,如下所述.


> **注意**由于性能差异,我们建议插件开发人员尽可能编写轻量级测试.


编写轻量级测试的标准方法是扩展以下类:


* [`LightPlatformCodeInsightFixtureTestCase`](upsource:///platform/testFramework/src/com/intellij/testFramework/fixtures/LightPlatformCodeInsightFixtureTestCase.java)用于没有任何Java依赖项的测试.

* [`LightCodeInsightFixtureTestCase`](upsource:///java/testFramework/src/com/intellij/testFramework/fixtures/LightCodeInsightFixtureTestCase.java)用于需要Java PSI或任何相关功能的测试.


编写光测试时,您可以指定测试中需要的项目要求,例如模块类型,配置的SDK,构面,库等.您可以通过扩展[`LightProjectDescriptor`]来实现( 
upsource:///platform/testFramework/src/com/intellij/testFramework/LightProjectDescriptor.java)类并从`LightCodeInsightFixtureTestCase.getProjectDescriptor()返回项目描述符.
在执行每个测试之前,如果测试用例返回与前一个项目描述符相同的项目描述符,则将重用该项目,或者如果描述符不同,则重新创建项目描述符.


> **注意**如果您需要为测试设置多模块项目,则必须编写繁重的测试.


多模块Java项目的设置代码如下所示:


```java
final TestFixtureBuilder<IdeaProjectTestFixture> projectBuilder = IdeaTestFixtureFactory.getFixtureFactory().createFixtureBuilder(getName());

// Repeat the following line for each module
final JavaModuleFixtureBuilder moduleFixtureBuilder = projectBuilder.addModule(JavaModuleFixtureBuilder.class);

myFixture = JavaTestFixtureFactory.getFixtureFactory().createCodeInsightFixture(projectBuilder.getFixture());
```


