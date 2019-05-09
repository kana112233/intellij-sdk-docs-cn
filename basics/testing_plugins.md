---
title: Testing Plugins
---

* IntelliJ Platform *代码库中的大多数测试都是*模型级功能测试*.
这意味着以下内容:


*测试在无头环境中运行,该环境使用大多数组件的实际生产实现,但许多UI组件除外.

*测试通常测试整个功能,而不是构成其实现的单个功能.

*测试不会测试Swing UI,而是直接使用底层模型.

*大多数测试将源文件或一组源文件作为输入数据,执行一项功能,然后将输出与预期结果进行比较.
结果可以指定为另一组源文件,作为输入文件中的特殊标记,或直接指定在测试代码中.


这种测试方法的最大好处是测试非常稳定,一旦编写完成就需要很少的维护,无论底层实现被重构或重写多少.


在经历了大量内部重构的15年以上的产品中,我们发现这种好处大大超过了测试执行速度较慢的缺点,并且与更加孤立的单元测试相比,更难以调试故障.


我们的测试方法的另一个结果是我们的测试框架没有提供:


*我们不提供推荐的模拟方法.
我们的代码库中有一些使用JMock的测试,但总的来说,我们发现很难模拟插件类需要具有的* IntelliJ Platform *组件的所有交互,我们建议使用实际组件.

*我们不提供Swing UI测试的通用框架.
您可以尝试使用[FEST](https://code.google.com/p/fest/)或[Sikuli](http://sikulix.com/)等工具进行插件用户界面测试,但我们不会
使用其中任何一种,不能提供任何使用指南.
在内部,我们使用手动测试来测试我们的Swing UI.


* [测试和夹具](/basics/testing_plugins/tests_and_fixtures.md)

* [轻型和重型测试](/basics/testing_plugins/light_and_heavy_tests.md)

* [测试项目和测试数据目录](/basics/testing_plugins/test_project_and_testdata_directories.md)

* [编写测试](/basics/testing_plugins/writing_tests.md)

* [测试突出显示](/basics/testing_plugins/testing_highlighting.md)


查看[本分步教程](/tutorials/writing_tests_for_plugins.md),教授如何编写和运行自定义语言插件的自动化测试(包括源代码).


