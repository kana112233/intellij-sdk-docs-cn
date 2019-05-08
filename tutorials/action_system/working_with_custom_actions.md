---
title: 1. Registering an Action
---

一个动作在技术上是一个类，派生自

[AnAction](upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnAction.java)

类。

要更新操作的状态，IntelliJ Platform框架将调用方法`AnAction.update(AnActionEvent)`。

类型的对象

[AnActionEvent](upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnActionEvent.java)

传递给此方法携带有关操作的当前上下文的信息，

特别是需要更新的具体表述。


### 1.1。
创建动作


要创建一个新的动作，我们需要扩展[AnAction](upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnAction.java)

类。
作为一个例子，我们将在`register_actions`代码示例中的`SimplePopDialogAction`类中执行此操作。


```java
public class SimplePopDialogAction extends AnAction {
}
```

**注意**`SimplePopDialogAction`没有任何类的类字段。
这是因为`AnAction`类的一个实例

存在于应用程序的整个生命周期中。
如果`AnAction`类使用字段来存储更短的数据

生命周期，并没有及时清除这些数据，数据将被泄露。
例如，存在任何“AnAction”数据

只有在`Project`的上下文中才会导致`Project`在用户关闭之后保存在内存中。


### 1.2。
覆盖actionPerformed()


[AnAction](upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnAction.java)

class是抽象的，扩展它的类必须覆盖`AnAction.actionPerformed(AnActionEvent)`方法。

此方法应包含在调用操作时要执行的代码。

在这种情况下，`SimplePopDialogAction.actionPerformed(AnActionEvent)`还没有做任何事情。


```java
public class SimplePopDialogAction extends AnAction {
  @Override
  public void actionPerformed(@NotNull AnActionEvent anActionEvent) {
    // Using the event, create and show a dialog
  }
}
```

### 1.3。
注册行动


要注册新创建的操作，应将“<action>”属性添加到插件配置文件的`<actions>`部分

[plugin.xml中](https://github.com/JetBrains/intellij-sdk-docs/blob/master/code_samples/register_actions/resources/META-INF/plugin.xml)。

IntelliJ IDEA具有嵌入式检查功能，可以发现未注册的操作。
以下是使用`SimplePopDialogAction`类的示例:


！[“动作从未使用过”检查](img/action_never_used.png)


要注册`SimplePopDialogAction`并设置其属性，请将插入符号放在操作声明上时按*** Alt + Enter ***。


然后填写** New Action **表单以设置`SimplePopDialogAction`的参数，例如:动作的名称和描述，

动作绑定的UI组件，动作绑定的菜单项的可视位置，以及调用动作的快捷方式。

在这种情况下，`SimplePopDialogAction`将在**工具菜单**中可用，它将被置于顶部，并且没有快捷方式。


！[新动作](img/new_action.png)


在这个例子中，在完成** New Action **表单并应用更改之后，插件的`plugins.xml`文件的`<actions>`部分

现在包含:


```xml
<actions>
    <!-- Add your actions here -->
    <action id="org.jetbrains.tutorials.actions.SimplePopDialogAction"
            class="org.jetbrains.tutorials.actions.SimplePopDialogAction" text="Simple Action"
            description="IntelliJ Action System Demo">
      <add-to-group group-id="ToolsMenu" anchor="first"/>
    </action>
</actions>
```
This declaration is adequate, but as we'll see in the next section there are more elements that can be added to the declaration.

### 1.4。
手动设置属性


您可以通过将操作添加到** New Action **表单或在plugin.xml文件中编辑其注册来配置操作的其他属性。

有关完整列表，请参阅[操作系统文档](../../basics/action_system.md#registrationing-actions)

支持的属性。


register_actions中`SimplePopDialogAction`的`<action>`声明

[plugin.xml中](https://github.com/JetBrains/intellij-sdk-docs/blob/master/code_samples/register_actions/resources/META-INF/plugin.xml)

file实际上包含`<keyboard-shortcut>`和`<mouse-shortcut>`的元素。
完整的声明是:


```xml
<action id="org.jetbrains.tutorials.actions.SimpleAction" class="org.jetbrains.tutorials.actions.SimplePopDialogAction"
        text="Simple Action" description="IntelliJ Action System Demo">
  <keyboard-shortcut first-keystroke="control alt A" second-keystroke="C" keymap="$default"/>
  <mouse-shortcut keystroke="control button3 doubleClick" keymap="$default"/>
  <add-to-group group-id="ToolsMenu" anchor="first"/>
</action>
```
The [plugin.xml](https://github.com/JetBrains/intellij-sdk-docs/blob/master/code_samples/register_actions/resources/META-INF/plugin.xml) 
file contains copious comments about the declaration.

执行上述步骤后，我们需要编译并运行插件到新创建的操作，作为工具菜单项:


！[“注册动作”快速修复](img/tools_menu_item_action.png)


### 1.5。
执行一个动作


为了使动作做一些事情，我们需要在`SimplePopDialoigAction.actionPerformed(AnActionEvent)`方法中添加代码。

以下代码从`anActionEvent`输入参数获取信息，并构造一个简单的消息对话框。

将显示一个通用图标，以及调用菜单操作中的`description`和`text`属性。


出于演示目的，`AnActionEvent.getData()`方法测试是否[Navigatable](upsource:///platform/core-api/src/com/intellij/pom/Navigatable.java)

对象是可用的，意味着例如
已在编辑器中选择了一个元素。
如果是这样，有关的信息

将所选元素机会性地添加到对话框中。


```java
@Override
public void actionPerformed(@NotNull AnActionEvent anActionEvent) {
    // Using the event, create and show a dialog
    Project currentProject = anActionEvent.getProject();
    StringBuffer dlgMsg = new StringBuffer(anActionEvent.getPresentation().getText() + " Selected!");
    String dlgTitle = anActionEvent.getPresentation().getDescription();
    // If an element is selected in the editor, add info about it.
    Navigatable nav = anActionEvent.getData(CommonDataKeys.NAVIGATABLE);
    if (nav != null) {
      dlgMsg.append(String.format("\nSelected Element: %s", nav.toString()));
    }
Messages.showMessageDialog(currentProject, dlgMsg.toString(), dlgTitle, Messages.getInformationIcon());
}
```

### 1.6。
设置操作的可见性和可用性


要控制操作的可见性和可用性，我们需要覆盖`AnAction.update(AnActionEvent)`方法。

此方法的默认实现不执行任何操作，这意味着始终禁用操作。

重写此方法以提供根据上下文动态更改操作的状态和(或)显示的功能。


在这个例子中，`SimplePopDialogAction.actionPerformed(AnActionEvent)`方法依赖于`Project`

对象可用。
因此`SimplePopDialogAction.update(AnActionEvent)`方法禁用

对于没有定义`Project`对象的上下文的操作:


```java
@Override
public void update(AnActionEvent anActionEvent) {
    // Set the availability based on whether a project is open
    Project project = anActionEvent.getProject();
    anActionEvent.getPresentation().setEnabledAndVisible(project != null);
}
```

参数`anActionEvent`包含有关调用位置和可用数据的信息。
注意`update()`方法

不检查[Navigatable](upsource:///platform/core-api/src/com/intellij/pom/Navigatable.java)对象是否可用

在启用`SimplePopDialogAction`之前。
这是为了演示代码的目的。


**注意**可以经常调用此方法:例如，如果将某个操作添加到工具栏，它将每秒更新两次。

这意味着这个方法应该_work fast_;
在这个阶段不应该做真正的工作。

例如，检查树或列表中的选择被认为是有效的，但是不使用文件系统。

如果你无法快速了解行动的状态，你应该在

[AnActionEvent](upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnActionEvent.java)

如果是这种情况，则通知用户该操作无法执行。


编译并运行插件项目并调用操作后，将弹出对话框:


！[已执行的操作](img/action_performed.png)


