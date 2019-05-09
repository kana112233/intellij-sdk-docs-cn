---
title: Documents
---

文档是可编辑的Unicode字符序列,通常对应于虚拟文件的文本内容.
文档中的换行符_always_规范化为`\ n`. 
* IntelliJ Platform *在透明地加载和保存文档时处理编码和换行符转换.


##如何获取文件？


*来自一个动作:`e.getData(PlatformDataKeys.EDITOR).getDocument()`

*来自虚拟文件:`FileDocumentManager.getDocument()`.
如果之前未加载文档内容,则此调用会强制从磁盘加载文档内容;
如果您只对打开的文档或可能已修改的文档感兴趣,请使用`FileDocumentManager.getCachedDocument()`.

*来自PSI文件:`PsiDocumentManager.getInstance().getDocument()`或`PsiDocumentManager.getInstance().getCachedDocument()`


##我可以用文件做什么？


您可以执行在“纯文本”级别上访问或修改文件内容的任何操作(作为字符序列,而不是Java元素树).


##文件来自何处？


当某些操作需要访问文件的文本内容时,会创建文档实例(特别是,需要为文件构建PSI).
此外,可以临时创建未链接到任何虚拟文件的文档实例,例如,表示对话框中文本编辑器字段的内容.


##文档持续多长时间？


来自`VirtualFile`实例的文档实例被弱引用.
因此,未经修改的[`Document`](upsource:///platform/core-api/src/com/intellij/openapi/editor/Document.java)实例可以被垃圾收集,如果它没有被任何人引用,
如果稍后再次访问文档内容,将创建一个新实例.
在插件的长期数据结构中存储`Document`引用将导致内存泄漏.


##如何创建文档？


如果你需要在磁盘上创建一个新文件,你不要创建一个`Document`:你创建一个PSI文件然后得到它的`Document`.
如果你需要创建一个没有绑定任何东西的`Document`实例,你可以使用`EditorFactory.createDocument`.


##如何在文档更改时收到通知？


*`Document.addDocumentListener`允许您接收有关特定`Document`实例中的更改的通知.

*`EditorFactory.getEventMulticaster().addDocumentListener`允许您接收有关所有打开文档中的更改的通知.

*在任何级别总线上订阅`AppTopics#FILE_DOCUMENT_SYNC`,以便在从磁盘保存或重新加载任何`Document`时接收通知.


##使用Documents有哪些规则？


一般读/写操作规则有效.
除此之外,任何修改文档内容的操作都必须包装在一个命令中(`CommandProcessor.getInstance().executeCommand()`).
可以嵌套`executeCommand()`调用,并将最外面的`executeCommand`调用添加到撤消堆栈中.
如果在命令中修改了多个文档,则撤消此命令将默认向用户显示确认对话框.


如果对应于“Document”的文件是只读的(例如,未从版本控制系统中检出),则文档修改将失败.
因此,在修改`Document`之前,必须调用`ReadonlyStatusHandler.getInstance(project).ensureFilesWritable()`来检查文件.


传递给`Document`修改方法(`setText`,`insertString`,`replaceString`)的所有文本字符串必须只使用\ n作为行分隔符.


##是否有可用于处理文档的实用程序？


[`DocumentUtil`](upsource:///platform/core-impl/src/com/intellij/util/DocumentUtil.java)包含`D​​ocument`处理的实用程序方法.
这允许您获取特定行的文本偏移等信息.
当您需要有关给定PsiElement的文本位置/偏移量信息时,这尤其有用.


