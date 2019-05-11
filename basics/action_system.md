---
title: Action System
---

## 执行和更新操作


The system of actions allows plugins to add their own items to IDEA menus and toolbars.  An action is a class, derived from the [`AnAction`](upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnAction.java) class, whose `actionPerformed` method is called when the menu item or toolbar button is selected.
For example, one of the action classes is responsible for the **File \| Open File...** menu item and for the **Open File** toolbar button.

操作被组织成组,而组又可以包含其他组.
一组动作可以形成工具栏或菜单.

该组的子组可以形成菜单的子菜单.


每个操作和操作组都有一个唯一标识符.
许多标准IDEA操作的标识符在[IdeActions](upsource:///platform/platform-api/src/com/intellij/openapi/actionSystem/IdeActions.java)类中定义.


每个操作都可以包含在多个组中,因此可以显示在IDEA用户界面中的多个位置.
可以出现动作的不同位置由[`ActionPlaces`](upsource:///platform/platform-api/src/com/intellij/openapi/actionSystem/ActionPlaces.java)界面中的常量定义.
对于动作出现的每个地方,都会创建一个新的[`Presentation`](upsource:///platform/platform-api/src/com/intellij/ide/presentation/Presentation.java).
因此,当相同的动作出现在用户界面的不同位置时,它可以具有不同的文本或图标.
通过复制`AnAction.getTemplatePresentation()`方法返回的表示来创建动作的不同表示.


为了更新动作的状态,IDEA定期调用方法`AnAction.update()`.
传递给此方法的[`AnActionEvent`](upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnActionEvent.java)对象包含有关操作的当前上下文的信息,
特别是需要更新的具体表述.


要检索有关IDE当前状态的信息,包括活动项目,所选文件,编辑器中的选择等,可以使用方法“AnActionEvent.getData()”.
可以传递给该方法的不同数据键在[`CommonDataKeys`](upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem/CommonDataKeys.java)类中定义.


`AnActionEvent`实例也传递给`actionPerformed`方法.


有关定义操作的分步演练,请查看[操作系统教程](/tutorials/action_system.md).


## 注册操作


注册动作有两种主要方式:通过在`plugin.xml`文件的`<actions>`部分中列出它,或者通过Java代码.


### 在plugin.xml中注册操作


以下示例演示了在`plugin.xml`中注册操作. 
`plugin.xml`的示例部分演示了可以在`<actions>`部分中使用的所有元素,并描述了每个元素的含义.


```xml
<!-- Actions -->
<actions>
  <!-- The <action> element defines an action to register.
       The mandatory "id" attribute specifies an unique 
       identifier for the action.
       The mandatory "class" attribute specifies the
       full-qualified name of the class implementing the action.
       The mandatory "text" attribute specifies the text of the
       action (tooltip for toolbar button or text for menu item).
       The optional "use-shortcut-of" attribute specifies the ID
       of the action whose keyboard shortcut this action will use.
       The optional "description" attribute specifies the text
       which is displayed in the status bar when the action is focused.
       The optional "icon" attribute specifies the icon which is
       displayed on the toolbar button or next to the menu item. -->
  <action id="VssIntegration.GarbageCollection" class="com.foo.impl.CollectGarbage" text="Collect _Garbage" description="Run garbage collector" icon="icons/garbage.png">
    <!-- The <add-to-group> node specifies that the action should be added
         to an existing group. An action can be added to several groups.
         The mandatory "group-id" attribute specifies the ID of the group
         to which the action is added.
         The group must be implemented by an instance of the DefaultActionGroup class.
         The mandatory "anchor" attribute specifies the position of the
         action in the group relative to other actions. It can have the values
         "first", "last", "before" and "after".
         The "relative-to-action" attribute is mandatory if the anchor
         is set to "before" and "after", and specifies the action before or after which
         the current action is inserted. -->
    <add-to-group group-id="ToolsMenu" relative-to-action="GenerateJavadoc" anchor="after"/>
      <!-- The <keyboard-shortcut> node specifies the keyboard shortcut
           for the action. An action can have several keyboard shortcuts.
           The mandatory "first-keystroke" attribute specifies the first
           keystroke of the action. The key strokes are specified according
           to the regular Swing rules.
           The optional "second-keystroke" attribute specifies the second
           keystroke of the action.
           The mandatory "keymap" attribute specifies the keymap for which
           the action is active. IDs of the standard keymaps are defined as
           constants in the com.intellij.openapi.keymap.KeymapManager class. 
           The optional "remove" attribute in the second <keyboard-shortcut>
           element below means the specified shortcut should be removed from 
           the specified action.
           The optional "replace-all" attribute in the third <keyboard-shortcut>
           element below means remove all keyboard and mouse shortcuts from the specified 
           action before adding the specified shortcut.  -->
    <!-- Add the first and second keystrokes to all keymaps  -->
    <keyboard-shortcut keymap="$default" first-keystroke="control alt G" second-keystroke="C"/>
    <!-- Except to the "Mac OS X" keymap and its children -->
    <keyboard-shortcut keymap="Mac OS X" first-keystroke="control alt G" second-keystroke="C" remove="true"/>
    <!-- The "Mac OS X 10.5+" keymap and its children will have only this keyboard shortcut for this action.  -->
    <keyboard-shortcut keymap="Mac OS X 10.5+" first-keystroke="control alt G" second-keystroke="C" replace-all="true"/>
    <!-- The <mouse-shortcut> node specifies the mouse shortcut for the
           action. An action can have several mouse shortcuts.
           The mandatory "keystroke" attribute specifies the clicks and
           modifiers for the action. It is defined as a sequence of words
           separated by spaces: 
           "button1", "button2", "button3" for the mouse buttons;
           "shift", "control", "meta", "alt", "altGraph" for the modifier keys;
           "doubleClick" if the action is activated by a double-click of the button.
           The mandatory "keymap" attribute specifies the keymap for which
           the action is active. IDs of the standard keymaps are defined as
           constants in the com.intellij.openapi.keymap.KeymapManager class.
           The "remove" and "replace-all" attributes can also be used in
           a <mouse-shortcut> element. See <keyboard-shortcut> for documentation.  -->
    <mouse-shortcut keymap="$default" keystroke="control button3 doubleClick"/>
  </action>
  <!-- The <group> element defines an action group. <action>, <group> and 
       <separator> elements defined within it are automatically included in the group.
       The mandatory "id" attribute specifies an unique identifier for the action.
       The optional "class" attribute specifies the full-qualified name of
       the class implementing the group. If not specified,
       com.intellij.openapi.actionSystem.DefaultActionGroup is used.
       The optional "text" attribute specifies the text of the group (text
       for the menu item showing the submenu).
       The optional "description" attribute specifies the text which is displayed
       in the status bar when the group is focused.
       The optional "icon" attribute specifies the icon which is displayed on
       the toolbar button or next to the group.
       The optional "popup" attribute specifies how the group is presented in
       the menu. If a group has popup="true", actions in it are placed in a
       submenu; for popup="false", actions are displayed as a section of the
       same menu delimited by separators. -->
  <group class="com.foo.impl.MyActionGroup" id="TestActionGroup" text="Test Group" description="Group with test actions" icon="icons/testgroup.png" popup="true">
    <action id="VssIntegration.TestAction" class="com.foo.impl.TestAction" text="My Test Action" description="My test action"/>
    <!-- The <separator> element defines a separator between actions.
         It can also have an <add-to-group> child element. -->
    <separator/>
    <group id="TestActionSubGroup"/>
    <!-- The <reference> element allows to add an existing action to the group.
         The mandatory "ref" attribute specifies the ID of the action to add. -->
    <reference ref="EditorCopy"/>
    <add-to-group group-id="MainMenu" relative-to-action="HelpMenu" anchor="before"/>
  </group>
</actions>
```

##从代码注册操作


要从代码注册操作,需要两个步骤.


* 首先,必须将源自`AnAction`的类的实例传递给[ActionManager]的`registerAction`方法(upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem)
/ActionManager.java)类,用于将操作与ID相关联.

* 其次,需要将操作添加到一个或多个组中.
要按ID获取操作组的实例,必须调用`ActionManager.getAction()`并将返回的值强制转换为[DefaultActionGroup](upsource:///platform/platform-api/src/com/intellij) 
/openapi/actionSystem/DefaultActionGroup.java)类.


您可以使用以下过程创建一个在IDEA启动时注册操作的插件.


**在IDEA创业公司注册诉讼**


* 创建一个实现[`ApplicationComponent`](upsource:///platform/core-api/src/com/intellij/openapi/components/ApplicationComponent.java)接口的新类.

* 在这个类中,重写`getComponentName`,`initComponent`和`disposeComponent`方法.

* 在plugin.xml文件的`<application-components>`部分注册此类.


为了阐明上述过程,请考虑以下示例Java类“MyPluginRegistration”,它注册在自定义`TextBoxes`类中定义的操作,并将新菜单命令添加到主菜单上的* Window *菜单组:


```java
public class MyPluginRegistration implements ApplicationComponent {
  // Returns the component name (any unique string value).
  @NotNull public String getComponentName() {
    return "MyPlugin";
  }


  // If you register the MyPluginRegistration class in the <application-components> section of
  // the plugin.xml file, this method is called on IDEA start-up.
  public void initComponent() {
    ActionManager am = ActionManager.getInstance();
    TextBoxes action = new TextBoxes();

    // Passes an instance of your custom TextBoxes class to the registerAction method of the ActionManager class.
    am.registerAction("MyPluginAction", action);

    // Gets an instance of the WindowMenu action group.
    DefaultActionGroup windowM = (DefaultActionGroup) am.getAction("WindowMenu");

    // Adds a separator and a new menu command to the WindowMenu group on the main menu.
    windowM.addSeparator();
    windowM.add(action);
  }

  // Disposes system resources.
  public void disposeComponent() {
  }
}
```

请注意,[插件开发入门](/basics/getting_started.md)中描述了示例`TextBoxes`类.


要确保在IDEA启动时初始化插件,请对`plugin.xml`文件的`<application-components>`部分进行以下更改:


```xml
<application-components>
  <!-- Add your application components here -->
  <component>
    <implementation-class>MypackageName.MyPluginRegistration</implementation-class>
  </component>
</application-components>
```

## 从Actions构建UI


如果插件需要包含从其自己的用户界面中的一组操作构建的工具栏或弹出菜单,则可以通过[`ActionPopupMenu`](upsource:///platform/editor-ui-api/src/)来完成
com/intellij/openapi/actionSystem/ActionPopupMenu.java)和[`ActionToolbar`](upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem/ActionToolbar.java)类.
可以通过调用`ActionManager.createActionPopupMenu`和`ActionManager.createActionToolbar`来创建这些对象.
要从这样的对象获取Swing组件,只需调用getComponent()方法即可.


如果操作工具栏附加到特定组件(例如,工具窗口中的面板),则通常需要调用`ActionToolbar.setTargetComponent()`并将相关组件的实例作为参数传递.
这可确保工具栏按钮的状态取决于相关组件的状态,而不是IDE框架中当前的焦点位置.


