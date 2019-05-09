---
title: File-based Indexes
---

基于文件的索引基于Map/Reduce架构.
每个索引都有某种类型的键和某种类型的值.


关键是后来用于从索引中检索数据的内容.


*示例:*在单词索引中,键是单词本身.


该值是与索引中的键相关联的任意数据.


*示例:*在单词索引中,值是一个掩码,指示单词出现在哪个上下文中(代码,字符串文字或注释).


在最简单的情况下,当我们只需要知道某些数据存在于哪些文件中时,该值的类型为“Void”并且不存储在索引中.


当索引实现索引文件时,它接收文件的内容并将文件中找到的键的映射返回到关联的值.


当您访问索引时,指定您感兴趣的密钥并返回密钥出现的文件列表以及与每个文件关联的值.


##实现基于文件的索引


一个相当简单的基于文件的索引实现是[UI Designer绑定表单索引](upsource:///plugins/ui-designer/src/com/intellij/uiDesigner/binding/FormClassIndex.java).
请参考它作为一个例子来更好地理解这个主题.


每个特定的索引实现都是一个扩展[FileBasedIndexExtension]的类(upsource:///platform/indexing-api/src/com/intellij/util/indexing/FileBasedIndexExtension.java).
基于文件的索引应该在`<fileBasedIndex>`扩展点中注册.


基于文件的索引的实现包括以下主要部分:


*`getIndexer()`返回实际负责根据文件内容构建一组键/值对的索引器类.

*`getKeyDescriptor()`返回密钥描述符,负责比较密钥并以序列化二进制格式存储它们.


可能最常用的[`KeyDescriptor`](upsource:///platform/util/src/com/intellij/util/io/KeyDescriptor.java)实现是[`EnumeratorStringDescriptor`](upsource:///platform/util/src/com/intellij/util/io/EnumeratorStringDescriptor.java),用于以有效的方式存储标识符.

*`getValueExternalizer()`返回值序列化器,负责以序列化二进制格式存储值.

*`getInputFilter()`允许将索引限制为仅限于某组文件.

*`getVersion()`返回索引实现的版本.
如果当前版本与用于构建索引的索引实现的版本不同,则会自动重建索引.


如果您不需要将任何值与文件相关联(即您的值类型为Void),则可以使用[ScalarIndexExtension]简化实现(upsource:///platform/indexing-impl/src/com/intellij/util/indexing/ScalarIndexExtension.java)作为基类.


> **注意**`DataIndexer.map()`返回的数据必须仅依赖于传递给方法的输入数据,并且不能依赖于任何外部文件.
否则,当外部数据发生更改时,您的索引将无法正确更新,并且索引中将包含过时数据.


> **注意**请参阅`com.intellij.util.indexing.DebugAssertions`,了解如何在开发期间启用其他调试断言以断言正确的索引实现.


##访问基于文件的索引


通过[FileBasedIndex](upsource:///platform/indexing-api/src/com/intellij/util/indexing/FileBasedIndex.java)类访问基于文件的索引.


支持以下主要操作:


*`getAllKeys()`和`processAllKeys()`允许获取在作为指定项目一部分的文件中找到的所有键的列表.


> **注意**返回的数据保证包含在最新项目内容中找到的所有密钥,但也可能包含当前未在项目中找到的其他密钥.


*`getValues()`允许获取与特定键相关联的所有值,但不能获取找到它们的文件.

*`getContainingFiles()`允许获取遇到特定键的所有文件.

*`processValues()`允许迭代遇到特定键的所有文件,并同时访问关联的值.


> **注意**禁止嵌套索引访问,因为它可能导致死锁.
首先从索引A收集所有必要的数据,然后在访问索引B时处理结果.


##标准索引


* IntelliJ Platform *包含许多基于标准文件的索引.
插件开发人员最有用的索引是:


*词索引

*文件名索引


通常,应使用[`PsiSearchHelper`](upsource:///platform/indexing-api/src/com/intellij/psi/search/PsiSearchHelper.java)类的辅助方法间接访问单词索引.


第二个索引是[`FilenameIndex`](upsource:///platform/indexing-impl/src/com/intellij/psi/search/FilenameIndex.java).
它提供了一种快速查找与特定文件名匹配的文件的方法. 
[`FileTypeIndex`](upsource:///platform/indexing-impl/src/com/intellij/psi/search/FileTypeIndex.java)提供了类似的目标:它允许快速查找某种文件类型的所有文件.


