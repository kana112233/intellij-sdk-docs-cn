---
title: 3. Handling Editor Events
---

以下教程集旨在介绍由编辑器事件激活的操作。

IntelliJ Platform SDK提供了一组回调，用于处理与编辑器相关的事件。


[源代码](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/editor_basics)


## 3.1。
在编辑器中处理击键


[TypedActionHandler](upsource:///platform/platform-api/src/com/intellij/openapi/editor/actionSystem/TypedActionHandler.java)

interface用于实现在编辑器中键入的键的自定义处理。

下面的步骤显示了如何自定义编辑器的行为，并使其以不同的方式对键入作出反应，而不仅仅是在编辑器区域中显示键入的字符。


### 3.1.2实现* TypedActionHandler *


首先，我们需要实现一个实例

[TypedActionHandler](upsource:///platform/platform-api/src/com/intellij/openapi/editor/actionSystem/TypedActionHandler.java):


```java
public class MyTypedHandler implements TypedActionHandler {
    @Override
    public void execute(@NotNull Editor editor, char c, @NotNull DataContext dataContext) {
    }
}
```

### 3.1.3。
实现处理击键的逻辑


`public void execute(@NotNull Editor editor，char c，@ NotNull DataContext dataContext);`


方法应包含处理击键的主要逻辑部分。
每次按下一个键时都会调用它。

在下面的示例中，我们的类型处理程序在键击发生后在编辑器中的零偏移处插入一个字符串:


```java
public class MyTypedHandler implements TypedActionHandler {
    @Override
    public void execute(@NotNull Editor editor, char c, @NotNull DataContext dataContext) {
        final Document document = editor.getDocument();
        Project project = editor.getProject();
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                document.insertString(0, "Typed\n");
            }
        };
        WriteCommandAction.runWriteCommandAction(project, runnable);
    }
}
```

### 3.1.4。
设置* TypedActionHandler *


要在插件中启用* TypedActionHandler *的自定义实现，我们需要创建它的新实例并传递给

`public TypedActionHandler setupHandler(TypedActionHandler handler)`方法

[TypedAction](upsource:///platform/platform-api/src/com/intellij/openapi/editor/actionSystem/TypedAction.java)

类。
通过这样做，我们用指定的处理程序替换打字处理程序。


```java
public class EditorIllustration extends AnAction {
    static {
        final EditorActionManager actionManager = EditorActionManager.getInstance();
        final TypedAction typedAction = actionManager.getTypedAction();
        typedAction.setupHandler(new MyTypedHandler());
    }
}
```

编译并运行上面的代码片段后，在编辑器中输入将在0位置插入一个额外的字符串。


## 3.2。
使用EditorActionHandler


类

[EditorActionHandler.java](upsource:///platform/platform-api/src/com/intellij/openapi/editor/actionSystem/EditorActionHandler.java)

用于编辑器中按键激活的操作。

以下步骤显示如何访问* EditorActionManager *并将其传递给要执行的操作。

在此示例中，我们将使用* EditorActionHandler *在当前插入符号下方插入一个额外的插入符号(如果适用)。


### 3.2.1。
前要求


创建一个动作:


```java
public class EditorHandlerIllustration extends AnAction {
    @Override                                        
    public void actionPerformed(@NotNull AnActionEvent anActionEvent) {
    }
    @Override
    public void update(@NotNull final AnActionEvent anActionEvent) {
    }
}
```

注册操作

[plugin.xml中](https://github.com/JetBrains/intellij-sdk-docs/blob/master/code_samples/editor_basics/resources/META-INF/plugin.xml):


```xml
<actions>
    <action id="EditorBasics.EditorHandlerIllustration" class="org.jetbrains.tutorials.editor.basics.EditorHandlerIllustration" text="Editor Handler"
            description="Illustrates how to plug an action in">
      <add-to-group group-id="EditorPopupMenu" anchor="first"/>
    </action>
</action>
```

### 3.2.2。
设置可见性


只有满足以下条件，我们才能看到我们的行动:

有一个项目打开，有一个编辑器可用，并且编辑器中至少有一个插入符号:


```java
public class EditorHandlerIllustration extends AnAction {
    @Override
    public void actionPerformed(@NotNull AnActionEvent anActionEvent) {
    }
    @Override
    public void update(@NotNull final AnActionEvent anActionEvent) {
        final Project project = anActionEvent.getData(CommonDataKeys.PROJECT);
        final Editor editor = anActionEvent.getData(CommonDataKeys.EDITOR);
        anActionEvent.getPresentation().setVisible((project != null && editor != null && editor.getCaretModel().getCaretCount() > 0));
    }
}
```

### 3.2.3。
获取* EditorActionHandler *


要自定义标准编辑器的操作，首先需要获取

一个例子

[EditorActionHandler](upsource:///platform/platform-api/src/com/intellij/openapi/editor/actionSystem/EditorActionHandler.java)

对于我们想要合作的行动。
在这种情况下，它是一个实例

[CloneCaretActionHandler](upsource:///platform/platform-impl/src/com/intellij/openapi/editor/actions/CloneCaretActionHandler.java)。


```java
public class EditorHandlerIllustration extends AnAction {
    @Override
    public void actionPerformed(@NotNull AnActionEvent anActionEvent) {
        final Editor editor = anActionEvent.getRequiredData(CommonDataKeys.EDITOR);
        EditorActionManager actionManager = EditorActionManager.getInstance();
        EditorActionHandler actionHandler = actionManager.getActionHandler(IdeActions.ACTION_EDITOR_CLONE_CARET_BELOW);
    }
    @Override
    public void update(@NotNull final AnActionEvent anActionEvent) {
        //...
    }
}
```

### 3.2.4。
使* EditorActionHandler *执行操作


要执行一个动作，我们需要调用`public final void execute(@NotNull Editor editor，@ Nullable final Caret contextCaret，final DataContext dataContext)`

相应的方法* EditorActionHandler *:


```java
public class EditorHandlerIllustration extends AnAction {
    @Override
    public void actionPerformed(@NotNull AnActionEvent anActionEvent) {
        final Editor editor = anActionEvent.getRequiredData(CommonDataKeys.EDITOR);
        EditorActionManager actionManager = EditorActionManager.getInstance();
        EditorActionHandler actionHandler = actionManager.getActionHandler(IdeActions.ACTION_EDITOR_CLONE_CARET_BELOW);
        actionHandler.execute(editor, editor.getCaretModel().getCurrentCaret(), anActionEvent.getDataContext());
    }
    @Override
    public void update(@NotNull final AnActionEvent anActionEvent) {
        //
    }
}
```

编译并运行以下代码示例后，将在当前活动的插入符号下面的编辑器中放置一个额外的插入符号。


[源代码](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/editor_basics)


