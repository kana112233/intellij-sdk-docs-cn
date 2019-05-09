---
title: Indexing and PSI Stubs
---

##指数


索引框架提供了一种定位某些元素的快捷方法,例如
在大型代码库中包含特定单词或具有特定名称的方法的文件.
插件开发人员可以使用IDE本身构建的现有索引,以及构建和使用自己的索引.


它支持两种主要类型的索引:


* [基于文件的索引](/basics/indexing_and_psi_stubs/file_based_indexes.md)

* [存根索引](/basics/indexing_and_psi_stubs/stub_indexes.md)


基于文件的索引直接构建在文件内容上.
存根索引是在序列化的*存根树*上构建的.
源文件的存根树是其PSI树的子集,其仅包含外部可见的声明,并以紧凑的二进制格式序列化.


查询基于文件的索引可以获得符合特定条件的文件集.
查询存根索引可以获得匹配的PSI元素集.
因此,自定义语言插件开发人员通常应在其插件实现中使用存根索引.


##哑模式


索引是一个潜在的漫长过程.
它是在后台执行的,在此期间,IDE的功能仅限于不需要索引的功能:基本文本编辑,版本控制等.此限制由[DumbService](upsource:///platform/core-api/src/com/intellij/openapi/project/DumbService.java).


`DumbService`提供API来查询IDE当前是处于“哑”模式(不允许索引访问)还是“智能”模式(所有索引都已构建并可以使用).
它还提供了延迟代码执行的方法,直到索引准备就绪.
有关更多详细信息,请参阅其javadoc.


## Gists


有时,以下条件成立:


*不需要基于文件的索引的聚合功能.
只需要根据特定文件的内容计算一些数据,并将其缓存在磁盘上

*不需要在索引期间急切地计算整个项目的数据(例如,它会降低索引速度,和/或所有项目文件的次要子集可能都需要此数据)

*可以根据要求懒散地重新计算数据,而不会造成重大的性能损失


在这种情况下,可以使用基于文件的索引,但文件列表提供了一种懒惰地执行数据计算,在磁盘上缓存以及更轻量级API的方法.
请参阅[VirtualFileGist](upsource:///platform/indexing-api/src/com/intellij/util/gist/VirtualFileGist.java)和[PsiFileGist](upsource:///platform/indexing-api/src/com/intellij/util/gist/PsiFileGist.java)文档.


