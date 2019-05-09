---
title: Structure View
---

用于特定文件类型的Structure View实现可以在许多级别上进行自定义.

如果自定义语言插件提供了一个实现

[StructureView](upsource:///platform/editor-ui-api/src/com/intellij/ide/structureView/StructureView.java)

接口,它可以完全用自定义用户界面组件替换标准结构视图实现.

但是,对于大多数语言而言,这不是必需的,也不是标准

[StructureView](upsource:///platform/editor-ui-api/src/com/intellij/ide/structureView/StructureView.java)

* IntelliJ Platform *提供的实现可以重用.


结构视图的起点是

[PsiStructureViewFactory](upsource:///platform/editor-ui-api/src/com/intellij/lang/PsiStructureViewFactory.java)

interface,在`com.intellij.lang.psiStructureViewFactory`扩展点中注册.


**例:**

[PsiStructureViewFactory](upsource:///plugins/properties/src/com/intellij/lang/properties/structureView/PropertiesStructureViewBuilderFactory.java)

对于

[属性语言插件](upsource:///plugins/properties)


重用* IntelliJ Platform *的实现

[StructureView](upsource:///platform/editor-ui-api/src/com/intellij/ide/structureView/StructureView.java),

插件返回一个

[TreeBasedStructureViewBuilder](upsource:///platform/editor-ui-api/src/com/intellij/ide/structureView/TreeBasedStructureViewBuilder.java)

来自它

[PsiStructureViewFactory.getStructureViewBuilder()](upsource:///platform/editor-ui-api/src/com/intellij/lang/PsiStructureViewFactory.java)<! - ＃L35-->

方法.

作为构建器的模型,插件可以指定子类

[TextEditorBasedStructureViewModel](upsource:///platform/editor-ui-api/src/com/intellij/ide/structureView/TextEditorBasedStructureViewModel.java),

通过重写此子类的方法,它可以自定义特定语言的结构视图.


**例**:

[StructureViewModel](upsource:///plugins/properties/properties-psi-impl/src/com/intellij/lang/properties/structureView/PropertiesFileStructureViewModel.java)

对于

[属性语言插件](upsource:///plugins/properties)


覆盖的主要方法是`getRoot()`,它返回实现的类的实例

[StructureViewTreeElement](upsource:///platform/editor-ui-api/src/com/intellij/ide/structureView/StructureViewTreeElement.java)

接口.

此接口没有标准实现,因此插件需要完全实现它.


结构视图树通常构建为PSI树的部分镜像.

在执行中

`StructureViewTreeElement.getChildren()`,

插件可以指定特定PSI树节点的哪些子元素需要在结构视图中表示为元素.

另一个重要的方法是`getPresentation()`,它可以用来自定义用于表示结构视图中元素的文本,属性和图标.


`StructureViewTreeElement.getChildren()`的实现需要与`TextEditorBasedStructureViewModel.getSuitableClasses()`匹配.

后一个方法返回一个'PsiElement` \ -derived类的数组,它们可以显示为结构视图元素,并用于在首次打开结构视图或从源代码“自动滚动”时选择与光标位置匹配的结构视图项. 
`选项被使用.


**例:**

[StructureViewElement](upsource:///plugins/properties/properties-psi-impl/src/com/intellij/lang/properties/structureView/PropertiesStructureViewElement.java)

对于

[属性语言插件](upsource:///plugins/properties/)


