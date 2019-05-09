---
title: Supporting multiple carets
---

＃＃ 介绍


IDEA 13.1中的编辑器实现增加了对多个独立插入符的支持.

大多数编辑器操作(键盘导航,文本插入和删除等)将独立应用于每个插入符号.

每个插入符号都有自己的关联选择,这是一系列连续的文档字符(可以为空).

当在一些动作之后,两个或更多个插入符号最终处于相同的视觉位置时,它们被合并为单个插入符号,其相关联的选择合并为单个插入符号.
当几个插入符号的选择重叠时会发生类似的事情:只剩下一个插入符号,并且选择将被合并.

有一个“主要”插入符号的概念 - 非多插入符号感知操作和需要单点文档上下文(如代码完成)的操作将在其中运行.

目前,最新的插入符号被认为是主要的插入符号.


##核心功能


可以通过以下方式获得与多插入符号实现相关的核心逻辑,例如访问当前存在的插入符号,添加和删除插入符号

[CaretModel](upsource:///platform/editor-ui-api/src/com/intellij/openapi/editor/CaretModel.java)

界面,也有一些变化

[selectionModel的](upsource:///platform/editor-ui-api/src/com/intellij/openapi/editor/SelectionModel.java)

接口.

检查这些接口的Javadoc以获取详细信息.


旧行为的显着变化:


*以前在`CaretModel`和`SelectionModel`中存在的方法来查询和修改插入符号和选择位置现在默认在主插入符号上工作.
但是,在`CaretModel.runForEachCaret`方法的上下文中,它们在当前的插入符号上运行.

因此遗留代码(不使用Caret接口)的行为将取决于其调用的上下文.


*块选择不再作为单独的概念存在.

相应地,SelectionModel接口中与块选择相关的方法已经改变了行为 - “hasBlockSelection()”将始终返回false,`setBlockSelection()`将创建一个等同于所请求的块选择的多插入符号选择.

`getBlockSelectionStarts()`和`getBlockSelectionEnds()`方法在多插入符号状态下工作,返回所有选定的区域.


##编辑器动作


### EditorAction和EditorActionHandler


什么时候

[EditorActionHandler](upsource:///platform/platform-api/src/com/intellij/openapi/editor/actionSystem/EditorActionHandler.java)被调用,一个额外的参数将被传递给它 - 一个它应该在它上面的插入符实例
如果在没有任何插入符号的情况下调用它,则操作或“null”.

如果处理程序调用另一个处理程序(同一个`actionId`的委托处理程序或一个完全不相关的处理程序),那么该参数通常应该传递给委托不变(除非没有向处理程序提供上下文插入符,但它需要调用另一个
特定插入符号的处理程序).

当然,如果处理程序的功能与插入符号/选择位置无关,则处理程序可以忽略该插入符号参数.


如果处理程序需要实现多插入符功能,它可以在重写的`doExecute`方法中显式地执行,但是如果它只需要为每个插入符号调用该方法,则将参数传递给`EditorActionHandler`构造函数就足够了
在没有特定插入符上下文的情况下调用处理程序时,`doExecute`为每个插入符号调用.


###编辑动作代表


以下代表可用:


*`EnterHandlerDelegate`

*`BackspaceHandlerDelegate`

*`JoinLinesHandlerDelegate`

*`EditorNavigationDelegate`

*`SmartEnterProcessor`

*`CommentCompleteHandler`

*`StatementUpDownMover`

*`CodeBlockProvider`


目前,无需对处理程序进行任何更改以支持多个插入符号 - 它们已针对每个插入符号进行调用.


##打字行动


### TypedActionHandler,TypedHandlerDelegate


[TypedActionHandler](upsource:///platform/platform-api/src/com/intellij/openapi/editor/actionSystem/TypedActionHandler.java)

和

[TypedHandlerDelegate](upsource:///platform/lang-api/src/com/intellij/codeInsight/editorActions/TypedHandlerDelegate.java)

对于每个键入的字符,仅调用一次实现.

如果这些处理程序需要支持多个插入符号,则需要明确地实现它.


[EditorModificationUtil](upsource:///platform/platform-api/src/com/intellij/openapi/editor/EditorModificationUtil.java).

在这种情况下,_typeInStringAtCaretHonorMultipleCarets_ utility方法可用于执行最常见的任务 - 将相同的文本插入所有插入位置和/或相对于其当前位置移动所有插入符号.

其用法示例:


* [TypedAction](upsource:///platform/platform-api/src/com/intellij/openapi/editor/actionSystem/TypedAction.java).


* [XmlGtTypedHandler](upsource:///xml/impl/src/com/intellij/codeInsight/editorActions/XmlGtTypedHandler.java).


-----------
**Note**:
Starting from IDEA 14,
[TypedHandlerDelegate](upsource:///platform/lang-api/src/com/intellij/codeInsight/editorActions/TypedHandlerDelegate.java)
implementations are invoked automatically for each caret. If one wants to implement custom multicaret behaviour on typing,
[TypedActionHandler](upsource:///platform/platform-api/src/com/intellij/openapi/editor/actionSystem/TypedActionHandler.java)
needs to be provided instead.

-----------

##代码洞察行动


继承自的现有操作

[CodeInsightAction](upsource:///platform/lang-api/src/com/intellij/codeInsight/actions/CodeInsightAction.java)仅适用于主插入符号.

为了支持多个插入符,我们应该继承

[MultiCaretCodeInsightAction](upsource:///platform/lang-impl/src/com/intellij/codeInsight/actions/MultiCaretCodeInsightAction.java)

相反(每个插入符可能有不同的编辑器和PSI实例,因此不可能使用旧的API).

自IDEA 14开始提供.


