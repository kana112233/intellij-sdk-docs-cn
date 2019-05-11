---
title: File and Class Choosers
---

## 文件选择器


要让用户选择文件，目录或多个文件，请使用

[FileChooser.chooseFiles()](upsource:///platform/platform-api/src/com/intellij/openapi/fileChooser/FileChooser.java)

方法。

此方法具有多个重载。

使用的最佳方法是返回void并将接收所选文件列表作为参数的回调。

这是唯一会在macOS上显示本机文件打开对话框的重载。


该

[FileChooserDescriptor](upsource:///platform/platform-api/src/com/intellij/openapi/fileChooser/FileChooserDescriptor.java)

class允许您控制可以选择的文件。

构造函数参数指定是否可以选择文件和/或目录，以及是否允许多个选择。

要对允许的选择进行更细粒度的控制，可以重载`isFileSelectable()`方法。

您还可以通过重载`getIcon()`，`getName()`和`getComment()`方法来自定义文件的表示。

[FileChooserDescriptor](upsource:///platform/platform-api/src/com/intellij/openapi/fileChooser/FileChooserDescriptor.java)。

请注意，本机Mac OS X文件选择器不支持大多数自定义，因此如果您依赖它们，则需要使用`chooseFiles()`的重载来显示标准的 *IntelliJ Platform* 对话框。


使用文件选择器的一种非常常见的方法是使用文本字段输入带有省略号按钮(“...”)的路径以显示文件选择器。

要创建这样的控件，请使用

[TextFieldWithBrowseButton](upsource:///platform/platform-api/src/com/intellij/openapi/ui/TextFieldWithBrowseButton.java)

组件并在其上调用`addBrowseFolderListener()`方法来设置文件选择器。

作为额外的好处，这将在文本框中输入路径时启用文件名完成。


用于选择文件的替代UI，通过键入其名称来选择文件的最常用方式时效果最佳，可通过以下方式获得

[TreeFileChooserFactory](upsource:///platform/lang-api/src/com/intellij/ide/util/TreeFileChooserFactory.java)类。


此API显示的对话框有两个选项卡:


* 一个显示项目结构


* Another显示的文件列表类似于`Goto File`弹出窗口所使用的文件。


要显示对话框，请在`createFileChooser()`返回的选择器上调用`showDialog()`，然后调用`getSelectedFile`来检索用户的选择。


## 类和包选择器


如果要为用户提供选择Java类的可能性，可以使用

[TreeClassChooserFactory](upsource:///java/openapi/src/com/intellij/ide/util/TreeClassChooserFactory.java)

类。

它的不同方法允许您指定从中获取类的范围，将选择限制为特定类的后代或接口的实现，以及从列表中包含或排除内部类。


要选择Java包，可以使用

[PackageChooserDialog](upsource:///java/java-impl/src/com/intellij/ide/util/PackageChooserDialog.java)

类。


