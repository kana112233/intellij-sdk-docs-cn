---
title: Writing Tests
---

在大多数情况下,一旦将必要的文件复制到测试项目并加载到内存编辑器中,编写测试本身就会涉及调用插件代码,并且对测试框架的依赖性很小.
但是,对于许多常见情况,框架提供了可以使测试更容易的辅助方法:


*`type()`模拟字符或字符串在内存编辑器中的输入.

*`performEditorAction()`模拟在内存编辑器的上下文中执行动作.

*`complete()`模拟代码完成的调用并返回完成列表中显示的查找元素列表(如果完成没有任何建议或一个自动插入的建议,则返回`null`).

*`findUsages()`模拟'Find Usages'的调用并返回找到的用法.

*`findSingleIntention()`与`launchAction()`结合使用指定名称模拟意图动作或检查quickfix的调用.

*`renameElementAtCaret()`或`rename()`模拟重命名重构的执行.


要将执行操作的结果与预期结果进行比较,可以使用`checkResultByFile()`方法.
具有预期结果的文件还可以包含标记,以指定预期的插入符号位置或选定的文本范围.
如果您正在测试修改多个文件的操作(例如,项目范围的重构),则可以使用“PlatformTestUtil.assertDirectoriesEqual()”将测试项目下的整个目录与预期输出进行比较.


