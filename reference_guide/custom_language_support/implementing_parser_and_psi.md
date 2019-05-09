---
title: Implementing a Parser and PSI
---

在IntelliJ平台中解析文件分为两步.

首先,构建抽象语法树(AST),定义程序的结构.

AST节点由IDE在内部创建,由实例表示

[ASTNode](upsource:///platform/core-api/src/com/intellij/lang/ASTNode.java)

类.

每个AST节点都有一个关联的元素类型

[IElementType](upsource:///platform/core-api/src/com/intellij/psi/tree/IElementType.java)

实例,元素类型由语言插件定义.

文件的AST树的顶级节点需要具有特殊的元素类型,实现

[IFileElementType](upsource:///platform/core-api/src/com/intellij/psi/tree/IFileElementType.java)

接口.


AST节点直接映射到底层文档中的文本范围.

AST的最底部节点匹配词法分析器返回的各个令牌,而更高级别的节点匹配多个令牌片段.

在AST树的节点上执行的操作(例如插入,移除,重新排序节点等)立即反映为对基础文档的文本的更改.


其次,PSI或程序结构接口树构建在AST之上,添加了用于操纵特定语言结构的语义和方法.

PSI树的节点由实现该节点的类表示

[PsiElement](upsource:///platform/core-api/src/com/intellij/psi/PsiElement.java)

接口,由语言插件创建

[ParserDefinition.createElement()](upsource:///platform/core-api/src/com/intellij/lang/ParserDefinition.java)

方法.

文件的PSI树的顶级节点需要实现

[PsiFile](upsource:///platform/core-api/src/com/intellij/psi/PsiFile.java)

接口,并在.中创建

[ParserDefinition.createFile()](upsource:///platform/core-api/src/com/intellij/lang/ParserDefinition.java)

方法.


**例**:

[ParserDefinition](upsource:///plugins/properties/properties-psi-impl/src/com/intellij/lang/properties/parsing/PropertiesParserDefinition.java)

对于

[属性语言插件](upsource:///plugins/properties)


PSI的生命周期在[Fundamentals](/platform/fundamentals.md)中有更详细的描述.


PSI实现的基类,包括

[PsiFileBase](upsource:///platform/core-impl/src/com/intellij/extapi/psi/PsiFileBase.java),

基础实现

[PsiFile](upsource:///platform/core-api/src/com/intellij/psi/PsiFile.java),

和

[ASTWrapperPsiElement](upsource:///platform/core-impl/src/com/intellij/extapi/psi/ASTWrapperPsiElement.java),

基础实现

[PsiElement](upsource:///platform/core-api/src/com/intellij/psi/PsiElement.java),

由* IntelliJ Platform *提供.


目前还没有现成的方法来重用现有的语言语法,例如,从ANTLR,用于创建自定义语言解析器.

解析器需要手动编码.


可以使用语法生成自定义语言解析器和PSI类

[Grammar-Kit](https://plugins.jetbrains.com/plugin/6606-grammar-kit)插件.

除了代码生成,它还提供了各种编辑语法文件的功能:语法高亮,快速导航,重构等.

Grammar-Kit插件使用自己的引擎构建,其源代码可以在上面找到

[GitHub的](https://github.com/JetBrains/Grammar-Kit).


语言插件提供了解析器实现作为的实现

[PsiParser](upsource:///platform/core-api/src/com/intellij/lang/PsiParser.java)

界面,从中返回

[ParserDefinition.createParser()](upsource:///platform/core-api/src/com/intellij/lang/ParserDefinition.java).

解析器接收一个实例

[PsiBuilder](upsource:///platform/core-api/src/com/intellij/lang/PsiBuilder.java)

class,用于从词法分析器获取标记流并保持正在构建的AST的中间状态.

解析器必须处理词法分析器返回的所有标记,直到流的末尾,换句话说直到

[PsiBuilder.getTokenType()](upsource:///platform/core-api/src/com/intellij/lang/PsiBuilder.java)

返回`null`,即使令牌根据语言语法无效.


**例**:

[PsiParser](upsource:///plugins/properties/properties-psi-impl/src/com/intellij/lang/properties/parsing/PropertiesParser.java)

实施

[属性语言插件](upsource:///plugins/properties/properties-psi-impl/src/com/intellij/lang/properties/).


解析器通过设置标记对来工作(

[PsiBuilder.Marker](upsource:///platform/core-api/src/com/intellij/lang/PsiBuilder.java)

实例)从词法分析器收到的令牌流中.

每对标记定义AST树中单个节点的词法分析器标记的范围.

如果一对标记嵌套在另一对中(在其开始之后开始并在结束之前结束),它将成为外部对的子节点.


设置结束标记时,将指定标记对和从中创建的AST节点的元素类型,这通过调用来完成

[PsiBuilder.Marker.done()](upsource:///platform/core-api/src/com/intellij/lang/PsiBuilder.java).

此外,可以在设置结束标记之前删除开始标记.

`drop()`方法只丢弃一个开始标记而不影响其后添加的任何标记,并且`rollbackTo()`方法删除开始标记和在其后添加的所有标记,并将词法分析器位置恢复为开始标记.

解析时,这些方法可用于实现前瞻.


方法

[PsiBuilder.Marker.precede()](upsource:///platform/core-api/src/com/intellij/lang/PsiBuilder.java)

当您在读取更多输入之前不知道在某个位置需要多少个标记时,从右到左解析很有用.

例如,二进制表达式“a + b + c”需要解析为`((a + b)+ c)`.

因此,在令牌“a”的位置处需要两个开始标记,但是直到读取令牌“c”才知道.

当解析器到达'b'后的'+'标记时,它可以调用`precede()`来复制'a'位置的开始标记,然后将其匹配的结束标记放在'c'之后.


一个重要的特征

[PsiBuilder](upsource:///platform/core-api/src/com/intellij/lang/PsiBuilder.java)

是处理空白和评论.

被视为空格或注释的标记类型由方法`getWhitespaceTokens()`和`getCommentTokens()`定义.

[ParserDefinition](upsource:///platform/core-api/src/com/intellij/lang/ParserDefinition.java)

类.

[PsiBuilder](upsource:///platform/core-api/src/com/intellij/lang/PsiBuilder.java)

从它传递给的标记流中自动省略空格和注释标记

[PsiParser](upsource:///platform/core-api/src/com/intellij/lang/PsiParser.java),

并调整AST节点的令牌范围,以便前导和尾随空格令牌不包含在节点中.


从中返回的令牌集

[ParserDefinition.getCommentTokens()](upsource:///platform/core-api/src/com/intellij/lang/ParserDefinition.java)

也用于搜索TODO项目.


为了更好地理解为简单表达式构建PSI树的过程,可以参考下图:


![PsiBuilder](IMG/PsiBuilder.gif)


一般来说,没有一种正确的方法可以为自定义语言实现PSI,插件作者可以选择PSI结构和方法集,这些方法对于使用PSI的代码最为方便(错误分析,重构等等)
上).

但是,有一个基本接口需要由自定义语言PSI实现使用,以支持重命名和查找用法等功能.

可以重命名或引用的每个元素(类定义,方法定义等)都需要实现

[PsiNamedElement](upsource:///platform/core-api/src/com/intellij/psi/PsiNamedElement.java)

接口,使用`getName()`和`setName()`方法.


可以在`com.intellij.psi.util`包中找到许多可用于实现和使用PSI的函数,特别是在

[PsiUtil](upsource:///java/java-psi-api/src/com/intellij/psi/util/PsiUtil.java)

和

[PsiTreeUtil](upsource:///platform/core-api/src/com/intellij/psi/util/PsiTreeUtil.java)

类.


调试PSI实现的一个非常有用的工具是

[PsiViewer插件](https://plugins.jetbrains.com/plugin/227-psiviewer).

它可以显示您的插件构建的PSI的结构,每个PSI元素的属性并突出显示其文本范围.


请参阅

[索引和PSI存根](/basics/indexing_and_psi_stubs.md)

对于高级主题.


