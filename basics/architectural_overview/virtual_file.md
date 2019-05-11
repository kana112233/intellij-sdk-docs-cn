---
title: Virtual Files
---

虚拟文件[com.intellij.openapi.vfs.VirtualFile](upsource:///platform/core-api/src/com/intellij/openapi/vfs/VirtualFile.java)是* IntelliJ平台的*文件表示
在文件系统(VFS)中.
最常见的是,虚拟文件​​是本地文件系统中的文件.
但是, *IntelliJ Platform* 支持多个可插入文件系统实现,因此虚拟文件也可以表示JAR文件中的类,从版本控制存储库加载的文件的旧版本,等等.


VFS级别仅处理二进制内容.
您可以将“VirtualFile”的内容作为字节流获取或设置,但编码和行分隔符等概念在较高的系统级别上处理.


## 如何获取虚拟文件？


*来自一个动作:`e.getData(PlatformDataKeys.VIRTUAL_FILE)`.
如果您对多重选择感兴趣,还可以使用`e.getData(PlatformDataKeys.VIRTUAL_FILE_ARRAY)`.

*来自本地文件系统中的路径:`LocalFileSystem.getInstance().findFileByIoFile()`

*来自PSI文件:`psiFile.getVirtualFile()`(如果PSI文件仅存在于内存中,则可能返回null)

*来自文档:`FileDocumentManager.getInstance().getFile()`


## 我能用它做什么？


可以使用典型的文件操作,例如遍历文件系统,获取文件内容,重命名,移动或删除.
应使用`VfsUtilCore.iterateChildrenRecursively`执行递归迭代,以防止由递归符号链接引起的无限循环.


## 它从何而来？


通过从项目根目录开始上下扫描文件系统,可以逐步构建VFS. 
VFS _refreshes_检测到文件系统中出现的新文件.
可以使用(`VirtualFileManager.getInstance().refresh()`或`VirtualFile.refresh()`以编程方式启动刷新操作.
每当文件系统观察者收到文件系统更改通知时(Windows和Mac操作系统上都可用),也会触发VFS刷新.


作为插件开发人员,如果需要访问刚通过IntelliJ Platform API由外部工具创建的文件,则可能需要调用VFS刷新.


## 虚拟文件持续多长时间？


在IDEA流程的整个生命周期中,磁盘上的特定文件由相同的`VirtualFile`实例表示.
可能存在与同一文件相对应的多个实例,并且它们可以被垃圾收集.
该文件是一个`UserDataHolder`,用户数据在这些相等的实例之间共享.
如果删除了一个文件,其对应的VirtualFile实例将变为无效(`isValid()`方法返回`false`,操作导致异常).


## 如何创建虚拟文件？


通常你没有.
通常,文件是通过PSI API或通过常规java.io.File API创建的.


如果确实需要通过VFS创建文件,可以使用`VirtualFile.createChildData()`方法创建`VirtualFile`实例,使用`VirtualFile.setBinaryContent()`方法将一些数据写入文件.


## 如何在VFS更改时收到通知？


`Virtual FileManager.addVirtual File Listener()`方法允许您接收有关VFS中所有更改的通知.


## 是否有用于分析和操作虚拟文件的实用程序？


[`VfsUtil`](upsource:///platform/core-api/src/com/intellij/openapi/vfs/VfsUtil.java)和[`VfsUtilCore`](upsource:///platform/core-api/src/com/intellij/openapi/vfs/VfsUtilCore.java)提供用于分析虚拟文件系统中的文件的实用程序方法.


您可以使用[`ProjectLocator`](upsource:///platform/core-api/src/com/intellij/openapi/project/ProjectLocator.java)查找包含给定虚拟文件的项目.


## 如何扩展VFS？


要提供备用文件系统实现(例如,FTP文件系统),请实现[VirtualFileSystem](upsource:///platform/core-api/src/com/intellij/openapi/vfs/VirtualFileSystem.java)类(
您很可能还需要实现`VirtualFile`),并将您的实现注册为[应用程序组件](/basics/plugin_structure/plugin_components.md).


要挂钩在本地文件系统中执行的操作(例如,如果您正在开发需要自定义重命名/移动处理的版本控制系统集成),请实现[LocalFileOperationsHandler](upsource:///platform/platform-api/src/com/intellij/openapi/vfs/LocalFileOperationsHandler.java)接口并通过`LocalFileSystem.registerAuxiliaryFileOperationsHandler`方法注册它.


## 使用VFS有哪些规则？


有关VFS体系结构和使用指南的详细说明,请参阅[IntelliJ平台虚拟文件系统](/basics/virtual_file_system.md).


