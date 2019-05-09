---
title: DialogWrapper
---

## DialogWrapper


该

[DialogWrapper](upsource:///platform/platform-api/src/com/intellij/openapi/ui/DialogWrapper.java)

是基本类，它应该用于* IntelliJ Platform *插件中显示的所有模态对话框(以及一些非模态对话框)。


它提供以下功能:


*按钮布局(特定于平台的“确定/取消”按钮，特定于macOS的“帮助”按钮)


*上下文帮助


*记住对话框的大小


*非模态验证(当输入对话框的数据无效时显示错误消息文本)


*  键盘快捷键:


*`Esc`用于关闭对话框


*“左/右”用于在按钮之间切换


*如果对话框中存在“是/否”动作，则为“Y/N”


*可选`不要再问'复选框


使用时

[DialogWrapper](upsource:///platform/platform-api/src/com/intellij/openapi/ui/DialogWrapper.java)

对于您自己的对话框，您需要按照以下步骤操作:


*调用基类构造函数，并在框架中提供将显示对话框的项目，或者提供对话框的父组件。


*从对话框类的构造函数中调用`init()`方法


*调用`setTitle()`方法设置对话框的标题


*实现`createCenterPanel()`方法以返回包含对话框主要内容的组件。


* *可选*:覆盖`getPreferredFocusedComponent()`方法并返回首次显示对话框时应该聚焦的组件。


* *可选*:重写`getDimensionServiceKey()`方法以返回将用于持久化对话框维度的标识符。


* *可选*:重写`getHelpId()`方法以返回与对话框关联的上下文帮助主题。


该

[DialogWrapper](upsource:///platform/platform-api/src/com/intellij/openapi/ui/DialogWrapper.java)

class通常与UI Designer表单一起使用。

在这种情况下，您将UI Designer表单绑定到您的类扩展

[DialogWrapper](upsource:///platform/platform-api/src/com/intellij/openapi/ui/DialogWrapper.java)，

将窗体的顶级面板绑定到一个字段，并从`createCenterPanel()`方法返回该字段。


要显示对话框，可以调用`show()`方法，然后使用`getExitCode()`方法检查对话框的关闭方式。


要自定义对话框中显示的按钮(替换标准的“OK/Cancel/Help”按钮组)，您可以覆盖`createActions()`或`createLeftActions()`方法。

这两个方法都返回一个Swing Action对象数组。

如果您要添加的按钮关闭对话框，则可以使用

[DialogWrapperExitAction](upsource:///platform/platform-api/src/com/intellij/openapi/ui/DialogWrapper.java)，

作为您行动的基类。

使用`action.putValue(DialogWrapper.DEFAULT_ACTION，true)`来设置默认按钮。


要验证输入到对话框中的数据，可以覆盖`doValidate()`方法。

该方法将由计时器自动调用。

如果当前输入的数据有效，则需要从实现中返回“null”。

否则，你需要返回一个

[ValidationInfo](upsource:///platform/platform-api/src/com/intellij/openapi/ui/ValidationInfo.java)

封装错误消息的类和与无效数据关联的可选组件。

如果指定一个组件，它旁边会显示一个错误图标，当用户尝试调用“OK”操作时它将被聚焦。


##示例


简单定义一个

[DialogWrapper](upsource:///platform/platform-api/src/com/intellij/openapi/ui/DialogWrapper.java):


```java
public class SampleDialogWrapper extends DialogWrapper {

    public SampleDialogWrapper() {
        super(true); // use current window as parent
        init();
        setTitle("Test DialogWrapper");
    }

    @Nullable
    @Override
    protected JComponent createCenterPanel() {
        JPanel dialogPanel = new JPanel(new BorderLayout());

        JLabel label = new JLabel("testing");
        label.setPreferredSize(new Dimension(100, 100));
        dialogPanel.add(label, BorderLayout.CENTER);

        return dialogPanel;
    }
}
```

用法

[DialogWrapper](upsource:///platform/platform-api/src/com/intellij/openapi/ui/DialogWrapper.java):


```java
JButton testButton = new JButton();
testButton.addActionListener(actionEvent -> {
  boolean result = new SampleDialogWrapper().showAndGet();
  if(result) {
    // user pressed ok
  }
});
```


