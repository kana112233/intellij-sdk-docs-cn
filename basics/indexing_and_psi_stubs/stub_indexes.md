---
title: Stub Indexes
---

## Stub Trees


存根树是文件的PSI树的子集;
它以紧凑的序列化二进制格式存储.
文件的PSI树可以由AST(通过解析文件的文本构建)或从磁盘反序列化的存根树来支持.
在两者之间切换是透明的.


存根树仅包含节点的子集.
通常,它仅包含从外部文件解析此文件中包含的声明所需的节点.
试图访问不属于存根树的任何节点,或者执行存根树无法满足的任何操作,例如:
访问PSI元素的文本,导致文件解析并将PSI切换到AST支持.


存根树中的每个存根都只是一个没有行为的bean类.
存根存储相应PSI元素状态的子集,如元素的名称,修饰符标志,如public或final等.存根还包含指向树中父节点的指针及其子节点的列表.


要支持自定义语言的存根,首先需要确定PSI树的哪些元素应存储为存根.
通常,您需要具有方法或字段之类的存根,这些存根可从其他文件中看到.
您通常不需要存在诸如语句或局部变量之类的存根,这些存根在外部是不可见的.


对于要存储在存根树中的每种元素类型,需要执行以下步骤:


*定义存根的接口,派生自[`StubElement`](upsource:///platform/core-api/src/com/intellij/psi/stubs/StubElement.java)接口([示例](upsource:///plugins/properties/properties-psi-api/src/com/intellij/lang/properties/psi/PropertyStub.java)).

*提供接口的实现([示例](upsource:///plugins/properties/properties-psi-impl/src/com/intellij/lang/properties/psi/impl/PropertyStubImpl.java)).

*确保PSI元素的接口扩展[`StubBasedPsiElement`](upsource:///platform/core-api/src/com/intellij/psi/StubBasedPsiElement.java)由存根接口的类型参数化([
示例](upsource:///plugins/properties/properties-psi-api/src/com/intellij/lang/properties/psi/Property.java)).

*确保PSI元素的实现类扩展了[`StubBasedPsiElementBase`](upsource:///platform/core-impl/src/com/intellij/extapi/psi/StubBasedPsiElementBase.java)参数化的存根类型
interface([示例](upsource:///plugins/properties/properties-psi-impl/src/com/intellij/lang/properties/psi/impl/PropertyImpl.java)<! -#L45-->).
提供接受`ASTNode`的构造函数和接受存根的构造函数.

*创建一个实现`IStubElementType`的类,并使用存根接口和实际的PSI元素接口进行参数化([example](upsource:///plugins/properties/properties-psi-impl/src/com/intellij/lang/properties/parsing/PropertyStubElementType.java)).
实现`createPsi()`和`createStub()`方法,用于从存根创建PSI,反之亦然.
实现`serialize()`和`deserialize()`方法,用于将数据存储在二进制流中.

*解析时使用实现`IStubElementType`的类作为元素类型常量([示例](upsource:///plugins/properties/properties-psi-impl/src/com/intellij/lang/properties/parsing/PropertiesElementTypes.java 
)).

*确保PSI元素接口中的所有方法在适当时访问存根数据而不是PSI树([example:Property.getKey()implementation](upsource:///plugins/properties/properties-psi-impl/src/com/intellij/lang/properties/psi/impl/PropertyImpl.java)<! -#L95  - >).


对于支持存根的每种语言,只需执行以下步骤一次:


*将您的语言的文件元素类型(从`ParserDefinition.getFileNodeType()`返回的元素类型)更改为扩展`IStubFileElementType`的类.

*在你的`plugin.xml`中,定义`<stubElementTypeHolder>`扩展并指定包含语言解析器使用的`IElementType`常量的接口([示例](upsource:///plugins/properties/src/META) 
-INF/plugin.xml中)<! -#L55  - >).


对于序列化字符串数据,例如
元素名称,在存根中,我们建议使用`StubOutputStream.writeName()`和`StubInputStream.readName()`方法.
这些方法确保每个唯一标识符仅在数据流中存储一次.
这减小了序列化存根树数据的大小.


如果您需要更改存根的存储二进制格式(例如,如果您想存储一些其他数据或一些新元素),请确保提升从您的语言`IStubFileElementType.getStubVersion()`返回的存根版本
.
这将导致重建存根和存根索引,并避免存储的数据格式与尝试加载它的代码之间的不匹配.


默认情况下,如果PSI元素扩展了“StubBasedPsiElement”,则该类型的所有元素都将存储在存根树中.
如果您需要更精确地控制存储哪些元素,请覆盖`IStubElementType.shouldCreateStub()`并为不应包含在存根树中的元素返回`false`.


> **注意**排除不是递归的:如果您返回false的元素的某些元素也是基于存根的PSI元素,它们将包含在存根树中.


确保存根树中存储的所有信息仅依赖于正在构建存根的文件的内容,并且不依赖于任何外部文件,这一点至关重要.
否则,当外部依赖关系发生更改时,将不会重建存根树,并且存根树中将存在过时且不正确的数据.


##存根索引


构建存根树时,您可以同时将关于存根元素的一些数据放入多个索引中,然后可以使用相应的键来查找PSI元素.
与基于文件的索引不同,存根索引不支持将自定义数据存储为值;
该值始终是PSI元素.
存根索引中的键通常是字符串(例如类名);
如果需要,还支持其他数据类型.


存根索引是扩展[`AbstractStubIndex`]的类(upsource:///platform/indexing-api/src/com/intellij/psi/stubs/AbstractStubIndex.java).
在最常见的情况下,当键类型为`String`时,使用更具体的基类,即[StringStubIndexExtension](upsource:///platform/indexing-api/src/com/intellij/psi/stubs/StringStubIndexExtension.java).
存根索引实现类在`<stubIndex>`扩展点中注册.


要将数据放入索引,请实现方法`IStubElementType.indexStub()`([example:JavaClassElementType.indexStub()](upsource:///java/java-psi-impl/src/com/intellij/psi/impl/java/stubs/JavaClassElementType.java)<! -#L189  - >).
此方法接受`IndexSink`作为参数,并为应存储元素的每个索引放入索引ID和键.


要从索引访问数据,请使用以下两种方法:


*`AbstractStubIndex.getAllKeys()`返回指定项目的指定索引中所有键的列表(例如,项目中找到的所有类名列表).

*`AbstractStubIndex.get()`返回与指定范围内的某个键(例如,具有指定短名称的类)对应的PSI元素的集合.


##相关论坛讨论


* [存根创建的生命周期](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206121959-Lifecycle-of-stub-creation/comments/206143885)


