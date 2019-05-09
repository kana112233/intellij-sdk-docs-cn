---
title: VCS Integration Plugins
---



此页面概述了IntelliJ IDEA 7.0中的版本控制集成API.

此处描述的大多数概念也适用于IntelliJ IDEA 6.0.

VCS API在版本6.0中进行了重大重构,本文档未描述旧版本的API.


＃＃ 关键概念


＃＃＃ 文件路径


A [FilePath](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/FilePath.java)

表示磁盘或VCS存储库中文件或目录的路径.

不像

[VirtualFile](upsource:///platform/core-api/src/com/intellij/openapi/vfs/VirtualFile.java),

一个

[文件路径(upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/FilePath.java)

可以表示磁盘上不存在的文件的路径.

一个主要区别

[文件路径(upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/FilePath.java)

和a

[java.io.File的](https://docs.oracle.com/javase/8/docs/api/java/io/File.html)

是那个

[文件路径(upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/FilePath.java)

缓存

[VirtualFile](upsource:///platform/core-api/src/com/intellij/openapi/vfs/VirtualFile.java)

对应于路径,因此可以在不进行VFS搜索的情况下检索它.


创建实例

[文件路径(upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/FilePath.java),

该

[VcsContextFactory](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/actions/VcsContextFactory.java)

使用API​​.

它可以作为`PeerFactory.getInstance()访问.getVcsContextFactory()`


[文件路径(upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/FilePath.java)

表示使用VCS存储库而不是本地路径中的路径

`VcsContextFactory.createFilePathOnNonLocal()`. 
`FilePath.isNonLocal()`为这些文件返回true.


###修订号


一个

[VcsRevisionNumber](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/history/VcsRevisionNumber.java)

表示文件的修订号.

如果VCS将修订号存储为简单整数,则为标准

[VcsRevisionNumber](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/history/VcsRevisionNumber.java).

可以使用Int实现.

如果VCS具有更复杂的版本号格式(如CVS,它使用一系列用点分隔的数字),则插件可以提供自定义实现.


### ContentRevision


一个

[ContentRevision](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/changes/ContentRevision.java)

表示文件的特定修订,它存在于本地或VCS存储库中.

它有三个主要属性:


*`FilePath`指定这是一个修订版的文件.
如果本地存在某个版本的文件,则应该是本地路径.


* [`VcsRevisionNumber`](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/history/VcsRevisionNumber.java)指定修订版的修订号,或者`
如果修订仅在本地存在,则为VcsRevisionNumber.NULL`.


*修订内容.


内容以字符串形式返回,VCS插件负责将二进制文件内容转换为正确的编码.

要根据IDE设置和字节顺序标记自动检测编码,可以使用方法`CharsetToolkit.bytesToString()`(此API是IDEA 7.0.2中的新增功能).

二进制文件的修订版也可以表示为BinaryContentRevision,它是ContentRevision的子类.

对于二进制修订版,getContent()的结果是未定义的,并且getBinaryContent()可用于将内容检索为字节数组.


可用于表示特定文件的当前磁盘版本的有用类是

[CurrentContentRevision](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/CurrentContentRevision.java).


### FileStatus


一个

[时间filestatus](upsource:///platform/editor-ui-api/src/com/intellij/openapi/vcs/FileStatus.java)

表示与VCS相关的文件状态(未版本控制,未更改,添加,修改等).

它确定用于在UI中呈现文件名称的颜色.


###改变


一个

[变更](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/changes/Change.java)

从VCS的角度来看,表示单个文件操作(创建,修改,移动/重命名或删除).

变更可以表示用户在本地执行但尚未提交的修改,已提交的修改或某种其他类型的修改(例如,搁置的更改或两个任意修订之间的差异).


一个

[变更](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/changes/Change.java)

基本上由两个内容修订组成:


*修订前(如果* Change *表示文件创建,则为“null”)


*修订后(如果* Change *代表文件删除,则为“null”)


移动或重命名由更改表示,其中修订前和修订后具有不同的文件路径.

可以为a指定自定义文件状态

[变更](upsource:///platform/vcs-api/vcs-api-core/src/com/intellij/openapi/vcs/changes/Change.java)

如果它表示文件的非标准修改(例如,已与冲突合并的文件).

如果尚未指定自定义文件状态,则会根据更改类型自动计算状态.


＃＃＃ 更改列表


一个

[变更表(upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/ChangeList.java)

表示一组命名的相关更改.

有两种主要的变更清单:


* [LocalChangeList](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/LocalChangeList.java)表示用户在本地完成的一组修改.
   
如果VCS也支持更改列表的概念(如Perforce那样),则VCS插件可以将IDEA的本地更改列表结构与VCS的结构同步.
   
否则,本地更改列表只是用户签出或修改的文件的子集.


* [CommittedChangeList](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/versionBrowser/CommittedChangeList.java)
   
表示签入VCS存储库的一组修改.
   
对于支持原子提交的VCS,每个提交的修订都由CommittedChangeList表示.
   
对于使用每个文件提交的VCS(如CVS),插件可以使用启发式方法将一系列单个文件提交分组为
   
[CommittedChangeList](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/versionBrowser/CommittedChangeList.java)


> **注意** * Changes *视图中的* Unversioned Files *,* Locally Deleted Files *等节点实际上不是更改列表,并且Change对象不表示这些节点下的文件.


##插件组件


本节描述构成VCS集成插件的不同组件,大致与它们应实现的顺序相同.


### AbstractVcs


这是VCS插件的主要入口点,IntelliJ平台使用该插件来检索插件提供的所有其他服务.

注册AbstractVcs实现的推荐方法是向plugin.xml添加扩展声明,如以下示例所示:


```xml
<idea-plugin>
  ...
  <extensions defaultExtensionNs="com.intellij">
    <vcs name="svn" vcsClass="org.jetbrains.idea.svn.SvnVcs"/>
  </extensions>
</idea-plugin>
```

这里`name`是VCS的唯一名称(这必须匹配你的`AbstractVcs.getName()`实现返回的字符串),* vcsClass *是你的实现类.


### ChangeProvider


该组件负责跟踪用户对工作副本的更改,并将这些更改报告给IntelliJ Platform核心.

从此返回此类的实现

[AbstractVcs.getChangeProvider()](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/AbstractVcs.java).


ChangeProvider与之协同工作

[VcsDirtyScopeManager](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/VcsDirtyScopeManager.java),

这是IntelliJ平台核心中的一个组件.

[VcsDirtyScopeManager](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/VcsDirtyScopeManager.java)

跟踪“脏范围” -  VCS文件状态可能已过期的文件集.

无论是在磁盘上修改文件还是通过显式调用使其VCS状态无效,都会将文件添加到脏范围

[VcsDirtyScopeManager.fileDirty()](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/VcsDirtyScopeManager.java)

要么

[VcsDirtyScopeManager.dirDirtyRecursively()](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/VcsDirtyScopeManager.java).


将一些文件添加到脏范围后,将传递脏范围

[ChangeProvider.getChanges()](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/ChangeProvider.java),

随着一个

[ChangelistBuilder](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/ChangelistBuilder.java)

实例,它作为一个接收器

[ChangeProvider](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/ChangeProvider.java)

提供有关已更改文件的数据.

此处理在后台线程中异步发生.


该

[ChangeProvider](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/ChangeProvider.java)

可以使用遍历脏范围内的所有文件

[VcsDirtyScope.iterate()](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/VcsDirtyScope.java),

或者使用`getDirtyFiles()`和`getDirtyDirectoriesRecursively()`方法检索有关其内容的信息.

如果可以批量检索VCS中有关本地更改的信息,则最好使用第二种方法,因为它可以更好地扩展到大型工作副本.


该

[ChangeProvider](upsource:///platform/vcs-api/src/com/intellij/openapi/vcs/changes/ChangeProvider.java)

使用以下方法将数据报告给ChangelistBuilder:


*`processChange()`用于已检出(或在VCS不使用显式检出模型时修改),计划添加或删除,移动或重命名的文件.


*`processUnversionedFile()`用于存在于磁盘上的文件,但不由VCS管理,未安排添加,也不通过* .cvsignore *或类似机制忽略.


*`processLocallyDeletedFile()`用于存在于VCS存储库中的文件,但不存在于磁盘上且未计划删除.


*`processIgnoredFile()`是针对不由VCS管理但通过* .cvsignore *或类似机制忽略的文件调用的.


*`processSwitchedFile()`用于文件或目录,对于这些文件或目录,工作副本对应于与其父目录的工作副本相比的不同分支.
   
可以为已调用processSwitchedFile()的相同文件调用此方法.




