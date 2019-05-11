---
title: Code Intentions
---

本主题描述了[conditional_operator_intention](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/conditional_operator_intention)，这是一个添加新[意图行动]的示例插件(https://www 
.jetbrains.com/help/idea/intention-actions.html)到IntelliJ平台意图列表。

此外，示例插件包含基于JUnit的测试。


##关于意向行动


** IntelliJ平台**分析您的代码并帮助处理可能导致错误的情况。

当怀疑存在可能的问题时，IDE会建议适当的意图操作，用特殊图标表示。

有关更多信息，请参阅** IntelliJ IDEA ** Web帮助中的[意图操作](https://www.jetbrains.com/help/idea/intention-actions.html)。


您可以使用IDE提供的[注意列表](https://www.jetbrains.com/help/idea/intention-actions.html#intention-settings)查看所有可用意图操作的列表。


**显示意向清单**


1.打开**设置**对话框。

2.在** IDE Settings **下，单击** Intentions **。
这将显示** IntelliJ IDEA **中当前可用的所有意图操作的列表。

 - 意图行动根据其使用区域分组。

 - 要启用/禁用意图操作，请选中/取消选中其左侧的复选框。


##使用的技术


[conditional_operator_intention](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/conditional_operator_intention)示例插件说明了以下技术的使用:


 - 如何分析[PSI树](/basics/architectural_overview/psi_files.md)。

 - 如何在PSI树中查找感兴趣的Java令牌。

 - 如何使用[PsiElementBaseIntentionAction](upsource:///platform/lang-api/src/com/intellij/codeInsight/intention/PsiElementBaseIntentionAction.java)类为游标下的标记元素调用快速修复操作。

 - 如何使用[IdeaTestFixtureFactory](upsource:///platform/testFramework/src/com/intellij/testFramework/fixtures/IdeaTestFixtureFactory.java)类为此插件创建JUnit测试。


##示例插件


** ConditionalOperatorConverter **示例插件位于`<％IntelliJ SDK Docs project％>/code_samples/conditional_operator_intention`目录中。

启动时，此插件将**转换三元运算符if语句**项添加到IDEA意图列表中的**条件运算符**节点:


![](IMG/IntentionsList.png)


####运行插件


**运行示例插件**


1.启动** IntelliJ IDEA **并打开保存在`<％IntelliJ SDK Docs project％>/code_samples/conditional_operator_intention`目录中的** conditionalOperatorConvertor **插件项目。

2.打开[项目结构](https://www.jetbrains.com/help/idea/project-structure-dialog.html)对话框，确保项目设置对您的环境有效。

3.如有必要，修改[运行/调试配置](https://www.jetbrains.com/idea/webhelp/run-debug-configuration-plugin.html)并选择** Run **运行插件
在主菜单上。


#### 它是如何工作的？


该插件分析在IDEA编辑器中打开的代码中光标下的符号。

如果光标位于“？”
条件运算符，** IntelliJ IDEA **建议用“if-then-else”语句替换此条件(三元)运算符:


![](IMG/TernaryOperator.png)


在这个例子中，代码:


```java
return (n>=0) ? n : -n;
```

将替换为代码:


```java
if ((n>=0)) {
    return n;
} else {
    return -n;
}
```

#####测试插件


示例插件包含`testSource/testPlugin /`包中的`YourTest` Java类和`testData /`目录中的测试数据。

要执行插件测试，请运行`YourTest.test()`方法。


有关测试和所有相关过程的详细信息，请参阅** IntelliJ IDEA ** Web帮助中的[测试](https://www.jetbrains.com/help/idea/performing-tests.html)。


