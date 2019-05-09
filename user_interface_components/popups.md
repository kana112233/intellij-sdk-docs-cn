---
title: Popups
---

＃＃ 弹出窗口


* IntelliJ Platform *用户界面广泛使用弹出窗口 - 没有镶边的半模式窗口(显式关闭按钮)，并在焦点丢失时自动消失。

在插件中使用这些控件可确保插件与IDE其余部分之间的一致用户体验。


弹出窗口可以选择显示标题，可选择移动和调整大小(并支持记住它们的大小)，并且可以嵌套(在选择项目时显示另一个弹出窗口)。


该

[JBPopupFactory](upsource:///platform/platform-api/src/com/intellij/openapi/ui/popup/JBPopupFactory.java)

界面允许您创建显示不同类型组件的弹出窗口，具体取决于您的特定需求。

最常用的方法是:


*`createComponentPopupBuilder()`是最通用的，允许你显示任何

[摇摆](https://docs.oracle.com/javase/tutorial/uiswing/start/index.html)

弹出窗口中的组件。


*`createListPopupBuilder()`创建一个弹出窗口，用于从a中选择一个或多个项目
    
[Swing JList](https://docs.oracle.com/javase/8/docs/api/javax/swing/JList.html)。


*`createConfirmation()`创建一个弹出窗口，用于在两个选项之间进行选择，并根据选择的选项执行不同的操作。


*`createActionGroupPopup()`创建一个弹出窗口，显示操作组中的操作并执行用户选择的操作。


除了普通箭头键之外，操作组弹出窗口支持从键盘选择操作的不同方法。

通过传递其中一个常量

[ActionSelectionAid](upsource:///platform/platform-api/src/com/intellij/openapi/ui/popup/JBPopupFactory.java)

枚举，您可以选择是否可以通过按下与其序列号对应的键，键入其文本的一部分(速度搜索)或按下助记符来选择操作。

对于具有固定项目集的弹出窗口，推荐的选择方法是顺序编号;

对于具有可变和可能大量项目的弹出窗口，速度搜索通常效果最佳。


如果你需要创建一个类似于列表的弹出窗口，它比简单的弹出窗口更灵活

[JList的](https://docs.oracle.com/javase/8/docs/api/javax/swing/JList.html)

但是不想将可能的选择表示为动作组中的动作，您可以直接使用

[ListPopupStep](upsource:///platform/platform-api/src/com/intellij/openapi/ui/popup/ListPopupStep.java)

界面和

[JBPopupFactory.createListPopup()](upsource:///platform/platform-api/src/com/intellij/openapi/ui/popup/JBPopupFactory.java)

方法。

通常您不需要实现整个界面;
相反，你可以从中得到

[BaseListPopupStep](upsource:///platform/platform-api/src/com/intellij/openapi/ui/popup/util/BaseListPopupStep.java)

类。

要覆盖的关键方法是`getTextFor()`(返回显示项目的文本)和`onChosen()`(当选择项时调用)。

通过从`onChosen()`方法返回一个新的弹出步骤，您可以实现分层(嵌套)弹出窗口。


一旦你创建了弹出窗口，你需要通过调用一个`show()`方法来显示它。

你可以让IntelliJ平台根据上下文自动选择位置，调用`showInBestPositionFor()`，或者通过`showUnderneathOf()`和`showInCenterOf()`等方法显式指定位置。


> **注意:**`show()`方法立即返回，不要等待弹出窗口关闭。


如果你需要在弹出窗口关闭时执行一些操作，你可以使用`addListener()`方法附加一个监听器，覆盖弹出内容的方法，如

[PopupStep.onChosen()](upsource:///platform/platform-api/src/com/intellij/openapi/ui/popup/PopupStep.java)，

或者在弹出窗口中将事件处理程序附加到您自己的组件。


