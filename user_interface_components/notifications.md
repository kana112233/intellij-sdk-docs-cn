---
title: Notifications
---

## 通知

其中一个主要的设计原则是避免使用模态消息框来通知用户有关可能需要用户注意的错误和其他情况.
作为替代,* IntelliJ Platform *提供了多个非模态通知UI选项.

### 对话

在模态对话框中工作时,不是在按下“OK”按钮时检查输入的有效性,而是通过模态对话框通知用户有关无效数据,建议的方法是使用
[DialogBu​​ilder.doValidate()](upsource:///platform/platform-api/src/com/intellij/openapi/ui/DialogBuilder.java)
这是前面描述的.

### 编辑提示

对于从编辑器调用的操作(例如重构,导航操作和不同的代码洞察功能),通知用户无法执行操作的最佳方法是使用
[HintManager](upsource:///platform/platform-api/src/com/intellij/codeInsight/hint/HintManager.java)类.
它的方法`showErrorHint()`在编辑器上方显示一个浮动弹出窗口,当用户开始在编辑器中执行另一个操作时,它会自动隐藏.
其他
[HintManager](upsource:///platform/platform-api/src/com/intellij/codeInsight/hint/HintManager.java)
方法可用于在编辑器上显示其他类型的非模态通知提示.


### 顶级通知

显示非模态通知的最常用方法是使用

[通知](upsource:///platform/platform-api/src/com/intellij/notification/Notification.java)类.

它有两个主要优点:

*  The user can control the way each notification type is displayed under `Settings | Notifications`

*  所有显示的通知都会在“事件日志”工具窗口中收集,以后可以查看

用于显示通知的具体方法是
[Notifications.Bus.notify()](upsource:///platform/platform-api/src/com/intellij/notification/Notification.java).
通知的文本可以包括HTML标记.
您可以通过在通知文本中包含超链接标记并传递一个来允许用户与通知进行交互
[的NotificationListener](upsource:///platform/platform-api/src/com/intellij/notification/NotificationListener.java)
实例到的构造函数
[通知](upsource:///platform/platform-api/src/com/intellij/notification/Notification.java)类.

The `groupDisplayId` parameter of the
[Notification](upsource:///platform/platform-api/src/com/intellij/notification/Notification.java)
constructor specifies a notification type.
The user can choose the display type corresponding to each notification type under `Settings | Notifications`.
To specify the preferred display type, you need to call
[Notifications.Bus.register()](upsource:///platform/platform-api/src/com/intellij/notification/Notification.java)
before displaying any notifications.

