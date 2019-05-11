---
title: Miscellaneous Swing Components
---

### 消息


该[邮件](upsource:///platform/platform-api/src/com/intellij/openapi/ui/Messages.java)

class提供了一种显示简单消息框，输入对话框(带文本字段的模态对话框)和选择器对话框(带组合框的模态对话框)的方法。

从名称中可以清楚地了解不同类别方法的功能。

在macOS上运行时，显示的消息框

[邮件](upsource:///platform/platform-api/src/com/intellij/openapi/ui/Messages.java)

class使用本机UI。


`showCheckboxMessageDialog()`函数提供了一种简单的方法来实现消息上的`Do not show this again`复选框。


请注意，建议在适当的时候使用非模态通知而不是模态消息框。

有关更多信息，请参阅[通知](notifications.md)主题。


### JBSplitter


该[JBSplitter](upsource:///platform/platform-api/src/com/intellij/ui/JBSplitter.java)
class是JetBrains的标准替代品
[调整JSplitPane](https://docs.oracle.com/javase/8/docs/api/javax/swing/JSplitPane.html)
类。

与其他一些JetBrains增强的Swing组件不同，它不是替代品，而是具有不同的API。
但是，为了获得一致的用户体验，建议使用
[JBSplitter](upsource:///platform/platform-api/src/com/intellij/ui/JBSplitter.java)
而不是标准[调整JSplitPane](https://docs.oracle.com/javase/8/docs/api/javax/swing/JSplitPane.html)
在你的插件中。


要将组件添加到拆分器，请调用`setFirstComponent()`和`setSecondComponent()`方法。
[JBSplitter](upsource:///platform/platform-api/src/com/intellij/ui/JBSplitter.java)
支持自动记忆拆分比例。
为了启用它，调用`setSplitterProportionKey()`方法并传递将存储比例的ID。


### JBTabs


该[JBTabs](upsource:///platform/platform-api/src/com/intellij/ui/tabs/JBTabs.java)
class是JetBrains实现的选项卡控件，用于编辑器选项卡和一些其他组件。
与标准Swing选项卡相比，它具有明显不同的外观和感觉，并且在macOS平台上看起来不那么原生，因此您可以选择哪个选项卡控件更适合您的插件。


