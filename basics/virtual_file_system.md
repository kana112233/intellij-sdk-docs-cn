---
title: Virtual File System
---

虚拟文件系统(VFS)是* IntelliJ Platform *的一个组件,它封装了用于处理文件的大部分活动.
它有以下主要用途:


*提供用于处理文件的通用API,无论其实际位置如何(在磁盘上,存档中,在HTTP服务器上等)

*在检测到修改时跟踪文件修改并提供文件内容的新旧版本.

*提供将附加持久数据与VFS中的文件相关联的可能性.


为了提供最后两个功能,VFS管理用户硬盘的某些内容的_peristent snapshot_.
快照仅存储通过VFS API至少请求过一次的文件,并且异步更新以匹配磁盘上发生的更改.


快照是应用程序级别,而不是项目级别 - 因此,如果某个文件(例如,JDK中的某个类)被多个项目引用,则其内容的一个副本将存储在VFS中.


所有VFS访问操作都通过快照.


如果通过VFS API请求某些信息但快照中没有这些信息,则会从磁盘加载并存储到快照中.
如果快照中有可用信息,则返回快照数据.
仅当访问了特定信息时,文件的内容和目录中的文件列表才存储在快照中 - 否则,仅存储名称,长度,时间戳,属性等文件元数据.


> **注意**这意味着IntelliJ平台UI中显示的文件系统状态和文件内容来自快照,快照可能并不总是与磁盘的实际内容相匹配.

>

>例如,在某些情况下,在IntelliJ平台选择删除之前,已删除的文件仍可在UI中显示一段时间.


在_refresh operations_期间,快照从磁盘更新,这通常是异步发生的.
通过VFS进行的所有写操作都是同步的 - 即内容立即保存到磁盘.


刷新操作将VFS的一部分状态与实际磁盘内容同步.
刷新操作由* IntelliJ Platform *或插件代码显式调用 - 即,当IDE运行时在磁盘上更改文件时,VFS不会立即获取更改. 
VFS将在下一次刷新操作期间更新,其中包括其范围内的文件.


*IntelliJ Platform* refreshes the entire project contents asynchronously on startup. By default, it performs a refresh operation when the user switches to it from another app, but users can turn this off via **Settings \| Appearance & Behavior \| System Settings \| Synchronize files on frame activation**.

在Windows,Mac和Linux上,* IntelliJ Platform *启动本机文件监视器进程,该进程从文件系统接收文件更改通知并将其报告给* IntelliJ Platform *.
如果文件观察程序可用,则刷新操作仅查看文件观察程序已报告已更改的文件.
如果不存在文件观察程序,则刷新操作将遍历刷新范围中的所有目录和文件.


刷新操作基于文件时间戳.
如果文件的内容已更改但其时间戳保持不变,则* IntelliJ Platform *将不会获取更新的内容.


目前没有用于从快照中删除文件的工具.
如果一个文件被加载一次,它将永远保留在那里,除非它从磁盘中删除并且在其父目录之一上调用了刷新操作.


The VFS itself does not honor ignored files listed in **Settings \| File Types \| Files** and folders to ignore and excluded folders listed in **Project Structure \| Modules \| Sources \| Excluded**. If the application code accesses them, the VFS will load and return their contents. In most cases, the ignored files and excluded folders must be skipped from processing by higher level code.

在IntelliJ平台IDE的运行实例的生命周期中,多个`VirtualFile`实例可能对应于同一磁盘文件.
它们是相同的,具有相同的`hashCode`并共享用户数据.


##同步和异步刷新


从调用者的角度来看,刷新操作可以是同步的也可以是异步的.
实际上,刷新操作是根据它们自己的线程策略执行的,而同步标志只是意味着调用线程将被阻塞,直到刷新操作(很可能在不同的线程上运行)完成.


同步刷新和异步刷新都可以从任何线程启动.
如果从后台线程启动刷新,则调用线程不能保持读取操作,否则会发生死锁.
有关线程模型和读/写操作的更多详细信息,请参阅[IntelliJ平台架构概述](/basics/architectural_overview/general_threading_rules.md).


相同的线程要求也适用于[LocalFileSystem.refreshAndFindFileByPath()](upsource:///platform/platform-api/src/com/intellij/openapi/vfs/LocalFileSystem.java)等函数,如果执行部分刷新,则执行部分刷新
快照中找不到具有指定路径的文件.


几乎在所有情况下,强烈使用异步刷新.
如果在刷新完成后需要执行某些代码,则应将代码作为`postRunnable`参数传递给其中一个刷新方法:
 

* [RefreshQueue.createSession()](upsource:///platform/platform-api/src/com/intellij/openapi/vfs/newvfs/RefreshQueue.java)<! -#L36-->

* [VirtualFile.refresh()](upsource:///platform/core-api/src/com/intellij/openapi/vfs/VirtualFile.java)<! -#L681-->
 

在某些情况下,同步刷新可能会导致死锁,具体取决于调用刷新操作的线程所持有的锁.


##虚拟文件系统事件


虚拟文件系统中发生的所有更改(由于刷新操作或由用户的操作引起)都会报告为_virtual file system events_. 
VFS事件始终在事件派发线程和写入操作中触发.


监听VFS事件的最有效方法是实现`BulkFileListener`接口并将其订阅到[VirtualFileManager.VFS_CHANGES](upsource:///platform/core-api/src/com/intellij/openapi/vfs/VirtualFileManager.java)<! - #L34  - >主题.


此API为您提供在一个列表中刷新操作期间检测到的所有更改,并允许您批量处理它们.
或者,您可以实现`VirtualFileListener`接口并使用[VirtualFileManager.addVirtualFileListener()]注册它(upsource:///platform/core-api/src/com/intellij/openapi/vfs/VirtualFileManager.java)<!--#L113  - >.
这将让您逐个处理事件.


> **注意** VFS监听器是应用程序级别,并将接收用户打开的* all *项目中发生的更改的事件.
您可能需要过滤掉与您的任务无关的事件.


在每次更改之前和之后都会发送VFS事件,您可以在之前的事件中访问该文件的旧内容.
请注意,刷新后发生的事件是在磁盘上发生更改后发送的 - 因此,当您处理`beforeFileDeletion`事件时,例如,该文件已从磁盘中删除.
但是,它仍然存在于VFS快照中,您可以使用VFS API访问其最后的内容.


请注意,刷新操作仅针对已在快照中加载的文件中的更改触发事件.
例如,如果您访问了一个目录的`VirtualFile`但从未使用[VirtualFile.getChildren()]加载其内容(upsource:///platform/core-api/src/com/intellij/openapi/vfs/VirtualFile.java)<! -#L135  - >,在该目录中创建文件时,可能无法获得`fileCreated`通知.


如果使用`VirtualFile.findChild()`仅在目录中加载单个文件,您将收到有关该文件更改的通知,但您可能无法获得同一目录中其他文件的创建/删除通知.


