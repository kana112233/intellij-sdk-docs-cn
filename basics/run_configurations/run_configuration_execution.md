---
title: Execution
---

标准执行运行操作将执行以下步骤:


* 用户选择*运行配置*(例如,通过从运行配置组合框中选择一个)和*执行器*(例如,通过按执行器创建的工具栏按钮).

* 通过轮询所有已注册的程序运行程序并询问是否可以使用指定的执行程序ID运行指定的运行配置文件,选择将实际执行该进程的*程序运行器*.

* 创建[`ExecutionEnvironment`](upsource:///platform/lang-api/src/com/intellij/execution/runners/ExecutionEnvironment.java)对象.
此对象聚合执行该过程所需的所有设置,以及所选的[`ProgramRunner`](upsource:///platform/lang-api/src/com/intellij/execution/runners/ProgramRunner.java).

* 调用`ProgramRunner.execute()`,接收执行程序和执行环境.


`ProgramRunner.execute()`的实现通过以下步骤来执行该过程:


*调用`RunProfile.getState()`方法创建[`RunProfileState`](upsource:///platform/lang-api/src/com/intellij/execution/configurations/RunProfileState.java)对象,描述一个过程
即将开始.
在此阶段,初始化命令行参数,环境变量和启动过程所需的其他信息.

* `RunProfileState.execute()`被调用.
它启动进程,将一个`ProcessHandler`附加到它的输入和输出流,创建一个控制台来显示进程输出,并返回一个[`ExecutionResult`](upsource:///platform/lang-api/src/com/intellij/execution/ExecutionResult.java)聚合控制台和进程处理程序的对象.

* 创建并调用`RunContentBuilder`对象,以在Run或Debug工具窗口的选项卡中显示执行控制台.


## 执行人


[`Executor`](upsource:///platform/lang-api/src/com/intellij/execution/Executor.java)接口描述了执行任何可能的运行配置的特定方式.


默认情况下, *IntelliJ Platform* 提供的三个默认执行程序是_Run _,_Debug_和带有Coverage_的_Run.
每个执行程序都有自己的工具栏按钮,它使用此执行程序启动选定的运行配置,以及使用此执行程序启动配置的自己的上下文菜单项.


作为插件开发人员,通常不需要实现`Executor`接口.
但是,它可能很有用,例如,如果您正在实现探查器集成并希望提供使用性能分析执行任何配置的可能性.


## 运行一个进程


每个运行配置实现都会出现`RunProfileState`接口,作为返回值`RunProfile.getState()`.
它描述了一个准备启动的进程,并保存了诸如命令行,当前工作目录和要启动的进程的环境变量之类的信息. 
(作为执行流程中单独步骤的`RunProfileState`的存在允许运行配置扩展和其他组件修补配置并在执行之前修改参数.)


用作`RunProfileState`实现的标准基类是[`CommandLineState`](upsource:///platform/lang-api/src/com/intellij/execution/configurations/CommandLineState.java).
它包含将正在运行的进程和控制台放在[`ExecutionResult`]中的逻辑(upsource:///platform/lang-api/src/com/intellij/execution/ExecutionResult.java),但不知道
这个过程实际上是如何开始的.
要启动该过程,最好使用[`GeneralCommandLine`](upsource:///platform/platform-api/src/com/intellij/execution/configurations/GeneralCommandLine.java)类,该类负责设置
命令行参数和执行过程.


或者,如果您需要运行的进程是基于JVM的进程,则可以使用[`JavaCommandLineState`](upsource:///java/execution/openapi/src/com/intellij/execution/configurations/JavaCommandLineState.java 
)基类.
它了解JVM的命令行参数,并可以处理计算JVM的类路径等细节.


要监视进程的执行并捕获其输出,通常使用[`OSProcessHandler`](upsource:///platform/platform-api/src/com/intellij/execution/process/OSProcessHandler.java)类.
一旦从命令行或Process对象创建了“OSProcessHandler”实例,就需要调用`startNotify()`方法来开始捕获其输出.
您可能还想将一个[`ProcessTerminatedListener`](upsource:///platform/platform-api/src/com/intellij/execution/process/ProcessTerminatedListener.java)附加到`OSProcessHandler`,以便退出状态为
该过程将显示在控制台中.


## 显示流程输出


如果您正在使用`CommandLineState`,将自动创建一个控制台视图并将其附加到该进程的输出.
或者,您可以自己安排:


*`TextConsoleBuilderFactory.createBuilder(project).getConsole()`创建一个[`ConsoleView`](upsource:///platform/lang-api/src/com/intellij/execution/ui/ConsoleView.java)实例

*`ConsoleView.attachToProcess()`将它附加到进程的输出.


如果您运行的进程使用ANSI转义码为其输出着色,则[`ColoredProcessHandler`](upsource:///platform/platform-api/src/com/intellij/execution/process/ColoredProcessHandler.java)类将
解析它并在IntelliJ控制台中显示颜色.


Console [filters](upsource:///platform/lang-api/src/com/intellij/execution/filters/Filter.java)允许您将流程输出中找到的某些字符串转换为可单击的超链接.
要将过滤器附加到控制台,请使用`CommandLineState.addConsoleFilters()`,或者,如果您手动创建控制台,请使用`TextConsoleBuilder.addFilter()`.


您可能想要重用的两个常见过滤器实现是[`RegexpFilter`](upsource:///platform/lang-api/src/com/intellij/execution/filters/RegexpFilter.java)和[`UrlFilter`](upsource:///platform/lang-api/src/com/intellij/execution/filters/UrlFilter.java).


## 从代码开始运行配置


如果您有一个需要执行的现有运行配置,最简单的方法是使用[`ProgramRunnerUtil.executeConfiguration()`](upsource:///platform/lang-impl/src/com/intellij/execution/ProgramRunnerUtil.java)<!--#L110  -->.
该方法采用[`Project`](upsource:///platform/core-api/src/com/intellij/openapi/project/Project.java),[`RunnerAndConfigurationSettings`](upsource:///platform/lang-api/src/com/intellij/execution/RunnerAndConfigurationSettings.java),以及[`Executor`](upsource:///platform/lang-api/src/com/intellij/execution/Executor.java)
.
要获取现有配置的`RunnerAndConfigurationSettings`,您可以使用,例如,`RunManager.getConfigurationSettings(ConfigurationType)`.
作为最后一个参数,您通常会传递`DefaultRunExecutor.getRunExecutorInstance()`或`DefaultDebugExecutor.getDebugExecutorInstance()`.


