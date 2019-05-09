---
title: Test Project and Testdata Directories
---

测试夹具创建一个*测试项目*环境.
除非您自定义项目创建,否则测试项目将有一个模块,其中一个源根名为`src`.
测试项目的文件存在于临时目录或内存文件系统中,具体取决于[`TempDirTestFixture`]的实现(upsource:///platform/testFramework/src/com/intellij/testFramework/fixtures
使用/TempDirTestFixture.java).


[`LightPlatformCodeInsightFixtureTestCase`](upsource:///platform/testFramework/src/com/intellij/testFramework/fixtures/LightPlatformCodeInsightFixtureTestCase.java)使用内存实现;
如果通过调用`IdeaTestFixtureFactory.createCodeInsightFixture`来设置测试环境,则可以指定要使用的实现.


> **注意**如果您的测试使用内存实现,并且中止测试的执行,则持久化文件系统缓存可能与内存结构不同步,并且您可能会在测试中出现虚假错误
.

>

>如果在一系列成功运行后遇到意外错误,**尝试再次运行测试**,如果这没有帮助,**删除沙箱目录中的“system”子目录**(通过`
用于Gradle设置的sandboxDirectory`或用于Devkit设置的* IntelliJ Platform * SDK设置).


在您的插件中,您通常会在`testdata`目录中存储测试的测试数据(例如将在其上执行插件功能的文件和预期的输出文件).
这只是插件内容根目录下的一个目录,但不在源根目录下. 
`testdata`中的文件通常不是有效的源代码,不得编译.


要指定`testdata`的位置,必须覆盖`LightPlatformCodeInsightFixtureTestCase.getTestDataPath()`方法.
默认实现假定作为* IntelliJ Platform *源代码树的一部分运行,不适用于第三方插件.


> **注意** * IntelliJ Platform *测试中一个非常常见的模式是使用正在执行的测试方法的名称作为构建`testdata`文件路径的基础.
这允许在测试相同功能的不同方面的不同测试方法之间重用大多数代码,并且还建议将此方法用于第三方插件测试.
可以使用`UsefulTestCase.getTestName()`检索测试方法的名称.


要将文件或目录从`testdata`目录复制到测试项目目录,可以使用[`CodeInsightTestFixture`]中的`copyFileToProject()`和`copyDirectoryToProject()`方法(upsource:///platform/testFramework/
src/com/intellij/testFramework/fixtures/CodeInsightTestFixture.java)类.


插件测试中的大多数操作都需要在内存编辑器中打开文件,其中将执行突出显示,完成和其他操作.
内存编辑器实例由`CodeInsightTestFixture.getEditor()`返回.
要将文件从`testdata`目录复制到测试项目目录并立即在编辑器中打开它,可以使用`CodeInsightTestFixture.configureByFile()`或`configureByFiles()`方法.
后者将多个文件复制到测试项目目录,并在内存编辑器中打开它们的* first *.


或者,您可以使用其他方法之一,这些方法带有用@@ TestDataFile注释的参数.
这些方法将指定文件从`testdata`目录复制到测试项目目录,打开内存编辑器中的第一个指定文件,然后执行请求的操作,如突出显示或代码完成.


在内存编辑器中打开文件时,文件内容中的特殊标记可用于指定插入符号位置或选择.
您可以使用以下标记之一:


*`<caret>`指定插入符号的位置.

*`<selection>`和`</selection>`指定要选择的文本范围的开始和结束.

*`<block>`和`</block>`指定列选择的起点和终点.


