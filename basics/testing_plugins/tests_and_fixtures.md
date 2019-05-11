---
title: Tests and Fixtures
---

 *IntelliJ Platform* 测试基础架构不依赖于任何特定的测试框架.
事实上,IntelliJ IDEA团队使用JUnit,TestNG和Cucumber来测试项目的不同部分.
但是,大多数测试都是使用JUnit 3编写的.


在编写自己的测试时,您可以选择使用标准基类为您执行测试设置和使用fixture类,这可以让您手动执行设置,而不会将您绑定到特定的测试框架.


使用前一种方法,您可以使用[`LightPlatformCodeInsightFixtureTestCase`]等类(upsource:///platform/testFramework/src/com/intellij/testFramework/fixtures/LightPlatformCodeInsightFixtureTestCase.java)(尽管它是最长和最丑陋的类之一)
您将在 *IntelliJ Platform*  API中遇到的名称,它实际上是编写测试的推荐方法).


使用后一种方法,您可以使用[`IdeaTestFixtureFactory`](upsource:///platform/testFramework/src/com/intellij/testFramework/fixtures/IdeaTestFixtureFactory.java)类为测试环境创建灯具实例,并且您
需要从测试框架使用的测试设置方法中调用fixture创建和设置方法.


