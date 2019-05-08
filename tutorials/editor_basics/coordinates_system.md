---
title: 2. Editor coordinates system. Positions and offsets
---

编辑器中的每个插入符都有一组描述其坐标的属性。

可以通过获取a来访问这些属性

[插入模型实例](upsource:///platform/editor-ui-api/src/com/intellij/openapi/editor/CaretModel.java)。

下面的示例将解释使用插入位置及其逻辑和视觉属性。


## 2.1。
前要求

通过操作执行对编辑器的访问。


## 2.2。
访问插入位置


要获得对插入位置的访问，应该获得“CaretModel”的实例。


```java
public class EditorAreaIllustration extends AnAction {
    @Override
    public void actionPerformed(AnActionEvent anActionEvent) {
        final Editor editor = anActionEvent.getRequiredData(CommonDataKeys.EDITOR);
        CaretModel caretModel = editor.getCaretModel();
    }
    @Override
    public void update(AnActionEvent e) {
       //...
    }
}
```

## 2.3。
逻辑位置


[LogicalPosition.java](upsource:///platform/editor-ui-api/src/com/intellij/openapi/editor/LogicalPosition.java)

表示插入符号的当前逻辑位置的行和列。
逻辑位置忽略折叠 - 

例如，如果文档的前10行被折叠，则文档中的第10行将在其逻辑位置具有行号10。


```java
public class EditorAreaIllustration extends AnAction {
    @Override
    public void actionPerformed(AnActionEvent anActionEvent) {
        final Editor editor = anActionEvent.getRequiredData(CommonDataKeys.EDITOR);
        CaretModel caretModel = editor.getCaretModel();
        LogicalPosition logicalPosition = caretModel.getLogicalPosition();
    }
    @Override
    public void update(AnActionEvent e) {
        //...
    }
}
```

逻辑位置可以存储定义其映射的其他参数

[VisualPosition.java](upsource:///platform/editor-ui-api/src/com/intellij/openapi/editor/VisualPosition.java)。

基本原理是单个逻辑对匹配由软包装引入的虚拟空间，即不同的视觉位置

可以对应于相同的逻辑位置。
在逻辑中存储精确的视觉位置细节很方便

位置是为了进一步简化“逻辑位置” - >“视觉位置”映射。


## 2.4。
视觉位置


[VisualPosition.java](upsource:///platform/editor-ui-api/src/com/intellij/openapi/editor/VisualPosition.java)

表示视觉位置并且可以与对应的逻辑位置不同。

视觉位置考虑折叠 - 例如，

如果文档的前10行被折叠，则文档中的第10行将在其视觉位置具有行号1。


```java
public class EditorAreaIllustration extends AnAction {
    @Override
    public void actionPerformed(AnActionEvent anActionEvent) {
        final Editor editor = anActionEvent.getRequiredData(CommonDataKeys.EDITOR);
        CaretModel caretModel = editor.getCaretModel();
        LogicalPosition logicalPosition = caretModel.getLogicalPosition();
        VisualPosition visualPosition = caretModel.getVisualPosition();
    }
    @Override
    public void update(AnActionEvent e) {
        //...
    }
}
```

## 2.5。
抵消


通过`CaretModel`也可以访问给定插入位置的绝对偏移量:


```java
public class EditorAreaIllustration extends AnAction {
    @Override
    public void actionPerformed(AnActionEvent anActionEvent) {
        final Editor editor = anActionEvent.getRequiredData(CommonDataKeys.EDITOR);
        CaretModel caretModel = editor.getCaretModel();
        LogicalPosition logicalPosition = caretModel.getLogicalPosition();
        VisualPosition visualPosition = caretModel.getVisualPosition();
        int offset = caretModel.getOffset();
    }
    @Override
    public void update(AnActionEvent e) {
        //...
    }
}
```

## 2.6。
显示位置值

要显示逻辑和视觉位置的实际值，我们添加一个

`Messages.showInfoMessage()`调用将在执行操作后以通知的形式显示它们。


```java
public class EditorAreaIllustration extends AnAction {
    @Override
    public void actionPerformed(AnActionEvent anActionEvent) {
        final Editor editor = anActionEvent.getRequiredData(CommonDataKeys.EDITOR);
        CaretModel caretModel = editor.getCaretModel();
        LogicalPosition logicalPosition = caretModel.getLogicalPosition();
        VisualPosition visualPosition = caretModel.getVisualPosition();
        int offset = caretModel.getOffset();
        Messages.showInfoMessage(logicalPosition.toString() + "\n" +
                        visualPosition.toString() + "\n" +
                        "Offset: " + offset, "Caret Parameters Inside The Editor");
    }
    @Override
    public void update(AnActionEvent e) {
           //...
    }
}
```

签出，编译和运行

[Editor Basics Plugin](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/editor_basics)，

然后移动插入符号，调用

[EditorAreaIllustration](https://github.com/JetBrains/intellij-sdk-docs/blob/master/code_samples/editor_basics/src/org/jetbrains/tutorials/editor/basics/EditorAreaIllustration.java)

动作，并看看逻辑和视觉位置如何依赖于折叠。


在上下文菜单中找到该操作:


![显示坐标动作](img/coordinates_action.png)


执行操作以查看插入位置:


![显示坐标动作](img/coordinates_demo.png)








