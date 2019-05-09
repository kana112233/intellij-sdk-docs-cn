---
title: General Threading Rules
---

##读/写锁


通常,* IntelliJ Platform *中与代码相关的数据结构由单个读取器/写入器锁覆盖.
这适用于PSI,VFS和项目根模型.


允许从任何线程读取数据.
从UI线程读取数据不需要任何特殊的努力.
但是,从任何其他线程执行的读取操作需要使用`ApplicationManager.getApplication().runReadAction()`或更短的`ReadAction.run/compute`包装在读取操作中.


只允许从UI线程写入数据,并且写操作总是需要用`ApplicationManager.getApplication().runWriteAction()`或更短的`WriteAction.run/compute`包装在一个写操作中.


此外,只允许从写安全上下文修改模型,其中包括用户操作,来自它们的`invokeLater`调用(参见下一节)和事务(`TransactionGuard.submitTransaction`).
您不能从UI渲染器或“SwingUtilities.invokeLater”调用中修改PSI,VFS或项目模型.
有关详细信息,请参阅`TransactionGuard`文档.


您不得在读取或写入操作之外访问模型.
不保证相应的对象在几个连续的读取动作之间存活.
因此,根据经验,每当您开始执行读取操作时,都应检查PSI/VFS/项目/模块是否仍然有效.


##`invokeLater`


要将控制从后台线程传递到事件调度线程,而不是标准的`SwingUtilities.invokeLater()`,插件应该使用`ApplicationManager.getApplication().invokeLater()`.
后一种API允许为调用指定_modality state_,即允许执行调用的模式对话框的堆栈.


*传递`ModalityState.NON_MODAL`意味着在关闭所有模态对话框后将执行操作.
请注意,如果任何打开(不相关)项目显示每个项目模式对话框,则在关闭对话框后将执行该操作.

*传递`ModalityState.stateForComponent()`意味着当最顶层显示的对话框是包含指定组件的对话框,或者是其父对话框之一时,可以执行该操作.

*如果没有传递模态状态,将使用`ModalityState.defaultModalityState()`.
在大多数情况下,这是最佳选择,在从UI线程调用时使用当前模态状态,并且具有对使用`ProgressManager`启动的后台进程的特殊处理:来自这样的进程的`invokeLater`可以在与该进程相同的对话框中运行
过程开始了.

*`ModalityState.any()`表示无论模态对话框如何,都会尽快执行runnable.
请注意,此类runnables禁止修改PSI,VFS或项目模型.
有关详细信息,请参阅`TransactionGuard`文档.


如果您的UI线程活动需要访问[基于文件的索引](../indexing_and_psi_stubs.md)(例如,它正在进行任何类型的项目范围PSI分析,解析引用等),请使用`DumbService#smartInvokeLater`.
这样,您的活动将在所有可能的索引过程完成后运行.


##后台进程和`ProcessCanceledException`


后台进程由`ProgressManager`类管理,它有很多方法来执行给定的代码

使用模态(对话框),非模态(在状态栏中可见)或不可见的进度.
在所有情况下,代码都是

在与“ProgressIndicator”对象关联的后台线程上执行.

可以通过`ProgressIndicatorProvider#getGlobalProgressIndicator`随时检索当前线程的指示符.


对于可见的进展,线程可以使用`ProgressIndicator`来通知用户当前状态:

例如
设置完成工作的文本或可见部分.


进度指示器还提供了用户处理取消后台进程的方法(按“取消”按钮),

或者来自代码(例如,当前操作由于项目中的某些变化而变得过时).

通过调用“ProgressIndicator#cancelout”可以将进度标记为取消.

该过程通过调用`ProgressIndicator#checkCanceled`(或者如果你手头没有指标实例,则为'ProgressManager#checkCanceled`)对此作出反应.

如果后台进程已被取消,则此调用将抛出一个特殊的未经检查的`ProcessCanceledException`.


使用PSI或其他类型的后台进程的所有代码都应该准备好从任何一点抛出的`ProcessCanceledException`.

永远不应该记录此异常,应该重新抛出它,并且它将在启动该过程的基础结构中处理.


应该经常调用`checkCanceled`以确保顺利取消该过程. 
PSI内部

内部有很多`checkCanceled`调用.
但是,如果您的过程执行冗长的非PSI活动,您可能需要

插入明确的`checkCanceled`调用,以便它经常发生,例如
在每个第N次循环迭代.


##防止UI冻结


后台线程不应该长时间执行读取操作.
原因是如果UI线程需要写操作(例如用户键入某些内容),它必须尽快获取它,否则UI将冻结,直到所有后台线程都已释放其读取操作.


最着名的方法是在出现写入操作时取消后台读取操作,然后从头开始重新启动后台读取操作.
编辑器突出显示,代码完成,Goto类/文件/等操作都像这样工作.

为了实现这一点,使用`ProgressIndicator`和一个特殊的监听器启动冗长的后台操作

写入操作开始时取消该指示符.

下一次后台线程调用`checkCanceled`时,将抛出`ProcessCanceledException`,

并且线程应该尽快停止其操作(并完成读取操作).
 

有两种建议的方法:


*如果您使用的是UI线程,请创建一个`ReadTask`并将其传递给`ProgressIndicatorUtils.schedule *`方法之一.
在`onCanceled`方法内部,如果应该重新启动活动,请再次安排它.

*如果您已经在后台线程中,请在循环中使用`ProgressManager.getInstance().runInReadActionWithWriteActionPriority()`,直到它通过或整个活动变得过时.


在这两种方法中,您应始终检查每个读取操作的开始,如果您正在使用的对象仍然有效,并且整个操作仍然有意义(即未被用户取消,则项目未关闭
等)


如果您正在进行的活动必须访问[基于文件的索引](../indexing_and_psi_stubs.md)(例如,它正在进行任何类型的项目范围的PSI分析,解析引用等),您应该重写`ReadTask#runBackgroundProcess 
`并在那里使用“智能模式”读取操作:`DumbService.getInstance(project).runReadActionInSmartMode(() - > performInReadAction(indicator))`


