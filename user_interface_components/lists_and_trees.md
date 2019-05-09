---
title: List and Tree Controls
---

### JBList和Tree


每当你通常使用标准时

[Swing JList](https://docs.oracle.com/javase/8/docs/api/javax/swing/JList.html)

组件，建议使用

[JBList](upsource:///platform/platform-api/src/com/intellij/ui/components/JBList.java)

作为直接替换的类。

[JBList](upsource:///platform/platform-api/src/com/intellij/ui/components/JBList.java)

支持以下附加功能

[JList的](https://docs.oracle.com/javase/8/docs/api/javax/swing/JList.html):


*如果项目不适合列表框宽度，则绘制带有项目完整文本的工具提示。


*当列表框中不包含任何项目时，在列表框中间绘制一条灰色文本消息。
   
可以通过调用`getEmptyText()。setText()`来定制文本。


*在列表框的右上角绘制一个忙碌图标，表示正在执行后台操作。
   
这可以通过调用`setPaintBusy()`来启用。


同样，

[树](upsource:///platform/platform-api/src/com/intellij/ui/treeStructure/Tree.java)

class提供了标准的替代品

[JTree的](https://docs.oracle.com/javase/8/docs/api/javax/swing/JTree.html)

类。

除了功能

[JBList](upsource:///platform/platform-api/src/com/intellij/ui/components/JBList.java)，

它支持宽选画(Mac风格)和拖放自动滚动。


### ColoredListCellRenderer和ColoredTreeCellRenderer


当您需要自定义列表框或树中的项目的表示时，建议使用

[ColoredListCellRenderer](upsource:///platform/platform-api/src/com/intellij/ui/ColoredListCellRenderer.java)

要么

[ColoredTreeCellRenderer](upsource:///platform/platform-api/src/com/intellij/ui/ColoredTreeCellRenderer.java)

类作为单元格渲染器。

这些类允许您通过调用`append()`从具有不同属性的多个文本片段中组合演示文稿，并通过调用`setIcon`为该项设置可选图标。

渲染器自动负责为所选项目和许多其他特定于平台的渲染详细信息设置正确的文本颜色。


### ListSpeedSearch和TreeSpeedSearch


为了便于在列表框或树中选择项目的基于键盘，您可以使用它在其上安装速度搜索处理程序

[ListSpeedSearch](upsource:///platform/platform-impl/src/com/intellij/ui/ListSpeedSearch.java)

和

[TreeSpeedSearch](upsource:///platform/platform-impl/src/com/intellij/ui/TreeSpeedSearch.java)。

这可以通过调用`new ListSpeedSearch(list)`或`new TreeSpeedSearch(tree)`来完成。

如果需要自定义用于定位元素的文本，可以覆盖`getElementText()`方法。

或者，您可以传递一个函数将项目转换为字符串。

函数需要作为`elementTextDelegate`传递给

[ListSpeedSearch](upsource:///platform/platform-impl/src/com/intellij/ui/ListSpeedSearch.java)

构造函数或作为`toString`来

[TreeSpeedSearch](upsource:///platform/platform-impl/src/com/intellij/ui/TreeSpeedSearch.java)

构造函数。


### ToolbarDecorator


插件开发中一个非常常见的任务是显示一个列表或树，允许用户添加，删除，编辑或重新排序项目。

该任务的实施极大地促进了

[ToolbarDecorator](upsource:///platform/platform-api/src/com/intellij/ui/ToolbarDecorator.java)

类。

此类提供了一个工具栏，其中包含对项目的操作，并且如果基础列表模型支持，则会自动启用对列表框中项目的拖放重新排序。

列表上方或下方的工具栏位置取决于运行IDE的平台。


要使用工具栏装饰器:


*如果您需要支持删除和重新排序列表框中的项目，请确保列表的模型实现
   
[EditableModel](upsource:///platform/util/src/com/intellij/util/ui/EditableModel.java)
   
接口。
   
[CollectionListModel](upsource:///platform/platform-api/src/com/intellij/ui/CollectionListModel.java)
   
是一个实现此接口的方便模型类。


*打电话
   
[ToolbarDecorator.createDecorator](upsource:///platform/platform-api/src/com/intellij/ui/ToolbarDecorator.java)
   
创建装饰器实例。


*如果需要支持添加和/或删除项目，请调用`setAddAction()`和/或`setRemoveAction()`。


*如果你需要除标准按钮之外的其他按钮，请调用`addExtraAction()`或`setActionGroup()`。


*调用`createPanel()`并将它返回的组件添加到面板中。


<! - 

### AbstractTreeBuilder和AbstractTreeStructure

TODO链接到教程

 - >


