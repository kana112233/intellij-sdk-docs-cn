---
title: Editor Components
---

## EditorTextField


相比

[Swing JTextArea](https://docs.oracle.com/javase/8/docs/api/javax/swing/JTextArea.html)，

* IntelliJ Platform的*编辑器组件具有很多优点:语法高亮支持，代码完成，代码折叠等等。

* IntelliJ Platform *编辑器通常显示在编辑器选项卡中，但它们也可以嵌入到对话框或工具窗口中。

这是由

[EditorTextField](upsource:///platform/platform-impl/src/com/intellij/ui/EditorTextField.java)

零件。


创建时

[EditorTextField](upsource:///platform/platform-impl/src/com/intellij/ui/EditorTextField.java)，

您可以指定以下属性:


*解析文本字段中文本的文件类型;


*文本字段是只读还是可编辑;


*文本字段是单行还是多行。


一个常见的用例

[EditorTextField](upsource:///platform/platform-impl/src/com/intellij/ui/EditorTextField.java)

输入Java类或包的名称。

这可以通过以下步骤完成:


*  使用
   
[JavaCodeFragmentFactory.getInstance().createReferenceCodeFragment()](upsource:///java/java-psi-api/src/com/intellij/psi/JavaCodeFragmentFactory.java)
   
创建表示类或包名称的代码片段;


*打电话
   
[PsiDocumentManager.getInstance().getDocument()](upsource:///platform/core-api/src/com/intellij/psi/PsiDocumentManager.java)
   
获取与代码片段对应的文档;


*将退回的文件传递给
   
[EditorTextField](upsource:///platform/platform-impl/src/com/intellij/ui/EditorTextField.java)
   
构造函数或其`setDocument()`方法。


