---
title: Code Formatter
---

IntelliJ平台包含一个用于实现自定义语言格式化程序的强大框架.

在此框架中,插件在不同语法元素之间的间距上指定* constraints *,并且IDE提供的格式化引擎计算需要对文件执行的最小空白修改数,以使其与约束匹配
.


格式化文件或文件片段的过程包括以下主要步骤:


* _formatting模型builder_(
   
[FormattingModelBuilder](upsource:///platform/lang-api/src/com/intellij/formatting/FormattingModelBuilder.java)
   
),由插件实现,提供格式化模型(
   
[FormattingModel](upsource:///platform/lang-api/src/com/intellij/formatting/FormattingModel.java)
   
)要格式化文档


*请求格式化模型构建文件的结构,以适应格式化,作为_blocks_的树(
   
[块](upsource:///platform/lang-api/src/com/intellij/formatting/Block.java)
   
)具有相关的缩进,包裹,对齐和间距设置.


*格式化引擎根据插件提供的格式化模型计算需要放置在每个块边界的空白字符(空格,制表符和/或换行符)的序列.


*请求格式化模型将计算的空白字符插入文件中的必要位置.


块的结构通常以这样的方式构建,即镜像文件的PSI结构 - 例如,在Java代码中,顶级格式化块覆盖整个文件,其子代覆盖文件中的各个类,块上
下一级覆盖类内部的方法,依此类推.
格式化程序仅修改块之间的字符,并且必须以这样的方式构建块树,使得底层块覆盖文件中的所有非空白字符:否则格式化程序可能会删除块之间的字符.


如果格式化操作不影响整个文件(例如,如果调用格式化程序来格式化粘贴的文本块),则不会构建完整的块树 - 而是仅构建格式化操作所涵盖的文本范围的块
他们的父母都是建造的.


对于每个块,插件指定以下属性:


* _spacing_(
   
[间距](upsource:///platform/lang-api/src/com/intellij/formatting/Spacing.java)
   
)指定在块的指定子节点之间插入的空格或换行符.
   
spacing对象指定必须放置在指定子块之间的最小和最大空间数,放置在那里的最小换行符数,以及是否应保留现有换行符和空行.
   
格式化模型还可以指定格式化程序不能修改指定块之间的间距.


*  The _indent_ specifies how the block is indented relative to its parent block.
   There are different modes of indenting defined by factory methods in the Indent class.
   The most commonly used are the none indent (which means the child block is not indented), the regular indent (the child block is indented by the number of spaces specified in the **Project Code Style \| General \| Indent** setting) and the continuation indent (based on **Project Code Style \| General \| Continuation Indent** setting).
   If the formatting model does not specify an indent, the "continuation without first" mode is used, which means that the first block in a sequence of blocks with that type is not indented and the following blocks are indented with a continuation indent.

* _wrap_(
   
[总结](upsource:///platform/lang-api/src/com/intellij/formatting/Wrap.java)
   
)指定块的内容是否包装到下一行.
   
通过在块内容之前插入换行符来执行换行.
   
插件可以指定特定块永远不会被包装,始终包装,或仅在超出右边距时进行包装.


* _alignment_(
   
[取向(upsource:///platform/lang-api/src/com/intellij/formatting/Alignment.java)
   
)指定哪些块应该彼此对齐.
   
如果具有设置为同一对象实例的alignment属性的两个块放在不同的行中,并且如果第二个块是其行中的第一个非空白块,则格式化程序在第二个块之前插入空格,以便它从
与第一列相同的列.


对于这些属性中的每一个,都存在许多特殊用途设置,这些设置在各个类的JavaDoc注释中进行了描述.

也可以看看

[SpacingBuilder](upsource:///platform/lang-api/src/com/intellij/formatting/SpacingBuilder.java)

这有助于构建基于规则的配置.


使用格式化程序的一个重要特例是当用户按下源代码文件中的“Enter”键时执行的智能缩进.

要确定新行的缩进,formatter引擎会在插入符号或该块的父节点之前的块上调用方法`getChildAttributes()`,具体取决于`isIncomplete()`方法的返回值.
插入符号之前.

如果光标前的块不完整(包含用户可能键入但尚未键入的元素,如参数列表的右括号或语句的尾部分号),则在块上调用`getChildAttributes()`
在插入之前;
否则,它会在父块上调用.


** IntelliJ IDEA 13中的新功能**:

可以通过[特殊注释](https://youtrack.jetbrains.com/issue/IDEA-56995#comment=27-605969)按区域抑制代码格式.


###代码样式设置


要指定插件提供的语言的默认缩进大小,并允许用户配置选项卡大小和缩进大小,您需要实现

[FileTypeIndentOptionsProvider](upsource:///platform/lang-api/src/com/intellij/psi/codeStyle/FileTypeIndentOptionsProvider.java)

接口并在`fileTypeIndentOptionsProvider`扩展点中注册实现.

`createIndentOptions()`的返回值确定默认的缩进大小.


###后悔者


** IntelliJ IDEA 12的新功能:**

允许自定义语言为语言插件支持的元素类型提供用户可配置的排列/分组规则.

可以通过修饰符和名称来细化规则,可以另外应用排序.

请参阅

[重排](upsource:///platform/lang-api/src/com/intellij/psi/codeStyle/arrangement/Rearranger.java)

和JavaDoc相关.


