---
title: Implementing Lexer
---

词法分析器,或

[词法分析器](https://en.wikipedia.org/wiki/Lexical_analysis),

定义文件内容如何分解为标记.

词法分析器可作为自定义语言插件的几乎所有功能的基础,从基本语法突出显示到高级代码分析功能.

词法分析器的API由.定义

[Lexer](upsource:///platform/core-api/src/com/intellij/lexer/Lexer.java)界面.


IDE在三个主要上下文中调用词法分析器,插件可以为这些上下文提供不同的词法分析器实现:


*语法高亮:词法分析器从执行中返回
   
[SyntaxHighlighterFactory](upsource:///platform/editor-ui-api/src/com/intellij/openapi/fileTypes/SyntaxHighlighterFactory.java)
   
在`com.intellij.lang.syntaxHighlighterFactory`扩展点中注册的接口.


*构建文件的语法树:期望从中返回词法分析器
   
[ParserDefinition.createLexer()](upsource:///platform/core-api/src/com/intellij/lang/ParserDefinition.java),
   
和
   
[ParserDefinition](upsource:///platform/core-api/src/com/intellij/lang/ParserDefinition.java)
   
interface在`com.intellij.lang.parserDefinition`扩展点中注册.


*构建文件中包含的单词的索引:
   
如果使用基于词法分析器的单词扫描器实现,则将词法分析器传递给
   
[DefaultWordsScanner](upsource:///platform/indexing-api/src/com/intellij/lang/cacheBuilder/DefaultWordsScanner.java)
   
构造函数.


可以逐步调用用于语法突出显示的词法分析器以仅处理文件的已更改部分,而在其他上下文中使用的词法分析器始终被调用以处理整个文件,或者以不同语言嵌入文件中的完整语言构造.


可以递增使用的词法分析器可能需要返回其*状态*,这意味着对应于文件中每个位置的上下文.

例如,a

[Java lexer](upsource:///java/java-psi-impl/src/com/intellij/lang/java/lexer/JavaLexer.java)

可以为顶级上下文,注释上下文和字符串文字上下文分别设置状态.

语法高亮词法分析器的一个重要要求是它的状态必须由返回的单个整数表示

[Lexer.getState()](upsource:///platform/core-api/src/com/intellij/lexer/Lexer.java).

那个州将被传递给

[Lexer.start()](upsource:///platform/core-api/src/com/intellij/lexer/Lexer.java)

当从文件中间恢复lexing时,方法以及要处理的片段的起始偏移量.

在其他上下文中使用的词法总是可以从`getState()`方法返回`0`.


为自定义语言插件创建词法分析器的最简单方法是使用[JFlex](https://jflex.de).

适配器类,

[FlexLexer](upsource:///platform/core-api/src/com/intellij/lexer/FlexLexer.java)

和

[FlexAdapter](upsource:///platform/core-api/src/com/intellij/lexer/FlexAdapter.java)

使JFlex词法分析器适用于IntelliJ平台Lexer API.

的源代码

[IntelliJ IDEA社区版](https://github.com/JetBrains/intellij-community)

包括位于* tools/lexer/jflex-1.4 *和lexer骨架文件* tools/lexer/idea-flex.skeleton *中的JFlex 1.4的修补版本,可用于创建兼容的词法分析器

[FlexAdapter](upsource:///platform/core-api/src/com/intellij/lexer/FlexAdapter.java).

修补后的JFlex版本提供了一个新的命令行选项`--charat`,它可以更改JFlex生成的代码,以便它可以与IntelliJ Platform骨架一起使用.

启用`--charat`选项将lexing的源数据作为a传递

[CharSequence的](https://docs.oracle.com/javase/8/docs/api/java/lang/CharSequence.html)

而不是一个字符数组.


对于使用JFlex开发词法分析器,

[JFlex支持](https://plugins.jetbrains.com/plugin/263-jflex-support)

插件可能很有用.

它提供语法突出显示和其他有用的功能,用于编辑JFlex文件.

[GrammarKit插件](https://plugins.jetbrains.com/plugin/6606-grammar-kit)

还内置了JFlex支持.


**注意:**

Lexers,特别是基于JFlex的词法分析器,需要以这样的方式创建:它们始终匹配文件的整个内容,令牌之间没有任何间隙,并为在其位置无效的字符生成特殊标记.

由于角色无效,Lexers绝不能过早地中止.


**例**:

[词法(upsource:///plugins/properties/src/com/intellij/lang/properties/parsing/Properties.flex)

的定义

[属性语言插件](upsource:///plugins/properties)


词法分析器的标记类型由实例定义

[IElementType](upsource:///platform/core-api/src/com/intellij/psi/tree/IElementType.java).

所有语言共有的许多令牌类型都在.中定义

[TokenType](upsource:///platform/core-api/src/com/intellij/psi/TokenType.java)

接口.

自定义语言插件应在适用的地方重用这些令牌类型.

对于所有其他令牌类型,插件需要创建新的

[IElementType](upsource:///platform/core-api/src/com/intellij/psi/tree/IElementType.java)

实例并与使用令牌类型的语言相关联.

相同

[IElementType](upsource:///platform/core-api/src/com/intellij/psi/tree/IElementType.java)

每次词法分析器遇到特定的令牌类型时,都应返回实例.


**例:**

[令牌类型](upsource:///plugins/properties/properties-psi-api/src/com/intellij/lang/properties/parsing/PropertiesTokenTypes.java)

对于

[属性语言插件](upsource:///plugins/properties)


可以在词法分析器级别实现的一个重要特性是在文件中混合语言,例如,在某些模板语言中嵌入Java代码片段.

如果一种语言支持将其片段嵌入另一种语言,则需要为可嵌入的不同类型的片段定义变色龙令牌类型,并且这些令牌类型需要实现

[ILazyParseableElementType](upsource:///platform/core-api/src/com/intellij/psi/tree/ILazyParseableElementType.java)

接口.

封闭语言的词法分析器需要将嵌入语言的整个片段作为单个变色龙标记返回,该标记由嵌入式语言定义.

要解析变色龙标记的内容,IDE将通过调用来调用嵌入语言的解析器

[ILazyParseableElementType.parseContents()](upsource:///platform/core-api/src/com/intellij/psi/tree/ILazyParseableElementType.java).


