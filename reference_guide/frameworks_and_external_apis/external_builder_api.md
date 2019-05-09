---
title: External Builder API and Plugins
---

###外部构建流程工作流程


当用户调用涉及执行外部构建(Make,Build Artifacts等)的操作时,会发生以下步骤:


*在IDE过程中执行编译前任务.


*在IDE过程中执行依赖于PSI的某些源生成任务(例如,UI设计器表单到源编译).


调用* [`BuildTargetScopeProvider`](upsource:///java/compiler/impl/src/com/intellij/compiler/impl/BuildTargetScopeProvider.java)扩展来计算外部构建的范围(构建目标的集合)
基于目标模块进行编译以及已知的一组更改).


*生成外部构建过程(或重用现有构建过程后台进程).


*外部构建过程加载项目模型(.idea,.iml文件等),由[`JpsModel`]表示(upsource:///jps/model-api/src/org/jetbrains/jps/model 
/JpsModel.java)实例.


*根据要编译的每个构建目标的依赖关系,计算要构建的完整目标树.


*对于每个目标,计算能够构建此目标的构建器集.


*对于每个目标和每个构建器,都会调用`build()`方法.
如果在设置中启用了“并行编译独立模块”选项,则可以并行执行此操作.
对于模块级构建器,为单个目标调用构建器的顺序由其类别决定;
对于其他构建者,订单未定义.


*保存用于记录编译状态的缓存.


*通过[`CompileContext`](upsource:///jps/jps-builders/src/org/jetbrains/jps/incremental/CompileContext.java)API报告的编译消息被传输到IDE进程并显示在UI中(
在* Messages *视图中).


*后编译任务在IDE过程中执行.


###增量构建


为了支持增量构建,构建过程使用许多缓存,这些缓存在构建调用之间保持不变.
即使您的编译器不支持增量构建,您仍需要报告正确的信息,以便增量构建可以正常地用于其他编译器.


* [`SourceToOutputMapping`](upsource:///jps/jps-builders/src/org/jetbrains/jps/builders/storage/SourceToOutputMapping.java)是源文件和输出文件之间的多对多关系(“
哪些源文件用于生成指定的输出文件“).
它通过调用`BuildOutputConsumer.registerOutputFile()`和`ModuleLevelBuilder.OutputConsumer.registerOutputFile()`来填充.


* [`Timestamps`](upsource:///jps/jps-builders/src/org/jetbrains/jps/incremental/storage/Timestamps.java)记录每个源文件在每个构建目标编译时的时间戳. 
(如果使用不同的范围多次调用编译并且同时更改了文件,则不同构建目标的最后编译时间戳可能会有所不同.)它由JPS自动更新.


IDE监视项目内容的更改,并使用这些缓存中的信息为每个编译生成一组脏文件和已删除文件. 
(需要重新编译脏文件,删除的文件需要删除其输出).
构建器还可以将其他文件报告为脏(例如,如果删除方法,构建器可以使用此方法将类报告为脏.)模块级构建器可以将一些文件添加到脏范围;
如果发生这种情况,并且构建器从其`build()`方法返回`ADDITIONAL_PASS_REQUIRED`,则将使用新的脏范围启动针对同一模块块的另一轮构建器执行.


构建器还可能希望使其自定义高速缓存存储其他信息以支持目标的部分重新编译(例如,模块中Java文件之间的依赖关系).
要存储此数据,您可以将任意文件存储在从`BuildDataManager.getDataPaths().getTargetDataRoot()`返回的目录中,或者使用更高级别的API:`BuildDataManager.getStorage()`


要在多个目标之间调用同一构建器之间传递自定义数据,可以使用`CompileContext.getUserData()`和`CompileContext.putUserData()`.


###外部构建器中的服务和扩展


外部构建器进程使用标准Java

[服务(https://docs.oracle.com/javase/8/docs/api/java/util/ServiceLoader.html)

支持插件的机制.
有几个服务接口(例如[`BuilderService`](upsource:///jps/jps-builders/src/org/jetbrains/jps/incremental/BuilderService.java),可以在插件中实现以扩展构建器功能.
需要通过创建包含实现类的限定名称的“META-INF/services/<service-interface-fqn>”文件来注册服务的实现.例如``BuilderService`实现在`META-INF/services中注册
/org.jetbrains.jps.incremental.BuilderService`文件.这些文件没有扩展名,因此您需要将相应的模式映射到IDE设置中的文本文件.


###注册外部构建器的插件


Sources of a plugin for External Builder should be put in a separate module. By convention such module has name '...-jps-plugin' and its sources are placed under 'jps-plugin' directory in the main plugin directory. Use `<compileServer.plugin>` extension to add the plugin to classpath of external build process, the plugin jar should be named `<jps module name>.jar`. 'Build' \| 'Prepare Plugin Module for deployment' action will automatically pack 'jps-plugin' part to a separate jar accordingly.

###调试外部构建器的插件


**如果您的测试IDE是IntelliJ IDEA 16.0或更高版本**


在测试IDE中打开“调试构建过程”切换操作(可通过“查找操作”获得).
在每次运行编译之后,构建过程将等待某个(随机)端口上的调试器连接,并在状态栏中显示端口号.
在IDE的工作副本中,应创建“远程”运行配置并指向此端口.
如果您经常需要调试外部构建器并希望重用创建的“远程”运行配置,则可以通过向插件运行配置添加以下VM选项来修复端口号:


```
-Dcompiler.process.debug.port=<port-number>
```

**如果您的测试IDE是IntelliJ IDEA 15.0或更早版本**


使用以下VM选项启动带有插件的IDE


```
-Dcompiler.process.debug.port=<port-number>
```

之后,每次在测试IDE中运行编译时,构建过程将等待此端口上的调试器连接,然后才继续.
在IDE的工作副本中,应创建“远程”运行配置并指向此端口.
指定端口“-1”将禁用调试模式.


###分析外部构建过程


构建过程具有内置的自我分析功能.
要启用它们,请执行以下操作:


1.将`<ide-home>/lib/yjp-controller-api-redist.jar`和`<ide-home>/bin/yjpagent.*`文件复制到`<ide-system-dir>/compile-server 
`


2. In "Settings \| Compiler \| Additional compiler process VM options" field enter `-Dprofiling.mode=true`

3.确保自动make已关闭


在此之后,每次构建过程运行都应该导致CPU快照存储在`<user-home>`/Snapshots目录中.

快照的名称类似于“ExternalBuild \  -  \ {date \} .snapshot”.


指定`-Dprofiling.mode = false`将关闭分析.

请捕获几个快照,以了解您认为构建应该比它更快地工作的情况.


请在问题跟踪器中创建问题,并将生成的\ * .snapshot文件附加到其中或将其上载到

[ftp://ftp.intellij.net/.uploads](ftp://ftp.intellij.net/.uploads)并指定问题中的链接.

还请提供有关您正在使用的构建过程的内存和其他VM设置的详细信息.


###访问外部构建过程的日志


日志文件位于目录下


```
<ide-system-directory>/log/build-log
```

可以找到`build-log.log`和`build-log.properties`文件.

`build-log.properties`是一个log4j配置文件,可以调整日志级别和所需的日志记录类别.

此文件包含来自所有构建会话的日志记录,包括来自auto-make的会话.


在版本14.1之前的IntelliJ平台版本中,log4j配置存储在`build-log.xml`中.


###从External Build访问项目模型和配置


外部构建过程中的项目模型由JPS(* JetBrains Project System *)提供.

项目由[`JpsProject`](upsource:///jps/model-api/src/org/jetbrains/jps/model/JpsProject.java)表示,模块由[`JpsModule`]表示(upsource:///jps/model-api/src/org/jetbrains/jps/model/JpsProject.java)等等.

如果您的编译器依赖于尚未添加到模型中的内容(例如某些构面设置),

你需要扩展JPS模型(使用`JpsGwtModuleExtension`作为参考实现)并提供实现

[`JpsModelSerializerExtension`](upsource:///jps/model-serialization/src/org/jetbrains/jps/model/serialization/JpsModelSerializerExtension.java)从项目文件加载配置.


####实施构建器


如果您的编译器不参与编译现有的[`BuildTarget`](upsource:///jps/jps-builders/src/org/jetbrains/jps/builders/BuildTarget.java),您需要创建一个新的实现
`BuildTarget`和`BuildTargetType`.
还在IDE端注册[`BuildTargetScopeProvider`](upsource:///java/compiler/impl/src/com/intellij/compiler/impl/BuildTargetScopeProvider.java)扩展的实现,以将所需目标添加到构建范围.

构建器实现应该扩展[`TargetBuilder`](upsource:///jps/jps-builders/src/org/jetbrains/jps/incremental/TargetBuilder.java)或[`ModuleLevelBuilder`](upsource:///jps/jps-builders/src/org/jetbrains/jps/incremental/ModuleLevelBuilder.java)类,应该使用[`BuilderService`]创建(upsource:///jps/jps-builders/src/org/jetbrains/jps/incremental/BuilderService.java)扩展.




