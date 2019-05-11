---
title: Run Configuration Management
---

本文档描述了使用运行配置和常见用例的主要类.


* 虚拟目录

{:TOC}


## 配置类型


实现任何运行配置类型的起点是[`ConfigurationType`](upsource:///platform/lang-api/src/com/intellij/execution/configurations/ConfigurationType.java)接口.
当用户打开_'Edit运行配置'对话框并执行_'Add'_动作时,将显示可用配置类型列表:


![创建](/basics/img/create-1.png)


每种类型都表示为[`ConfigurationType`]的实例(upsource:///platform/lang-api/src/com/intellij/execution/configurations/ConfigurationType.java),注册如下:


```xml
<configurationType implementation="org.jetbrains.plugins.gradle.service.execution.GradleExternalTaskConfigurationType" />
```

实现此接口的最简单方法是使用[`ConfigurationTypeBase`](upsource:///platform/lang-api/src/com/intellij/execution/configurations/ConfigurationTypeBase.java)基类.
为了使用它,您需要继承它并提供配置类型参数(ID,名称,描述和图标)作为构造函数参数.
除此之外,你需要调用[`addFactory()`](upsource:///platform/lang-api/src/com/intellij/execution/configurations/ConfigurationTypeBase.java)<!-- #L46-
 -->添加配置工厂的方法.


## 配置工厂


所有运行配置都是由为特定`ConfigurationType`注册的[`ConfigurationFactory`](upsource:///platform/lang-api/src/com/intellij/execution/configurations/ConfigurationFactory.java)创建的.
有一个`ConfigurationType` [有多个](upsource:///platform/lang-api/src/com/intellij/execution/configurations/ConfigurationType.java)<!-- #L34-->`ConfigurationFactory
`:


![配置工厂](/basics/img/create-3.png)


[`ConfigurationFactory`]的关键API(upsource:///platform/lang-api/src/com/intellij/execution/configurations/ConfigurationFactory.java),以及您需要实现的唯一方法是
[`createTemplateConfiguration`](upsource:///platform/lang-api/src/com/intellij/execution/configurations/ConfigurationFactory.java)<!-- #L45-->方法.
每个项目调用此方法一次以创建模板运行配置.


通过[`createConfiguration`]克隆模板来调用所有实际运行配置(从工作空间加载或由用户创建)(upsource:///platform/lang-api/src/com/intellij/execution/configurations/ConfigurationFactory.java)<!-- #L39  -->方法.


您可以通过覆盖[`getIcon`]来自定义配置工厂的其他方面(upsource:///platform/lang-api/src/com/intellij/execution/configurations/ConfigurationFactory.java)<!--#L59-
 -->,[`getAddIcon`](upsource:///platform/lang-api/src/com/intellij/execution/configurations/ConfigurationFactory.java)<!--#L55-->,[`getName`](
upsource:///platform/lang-api/src/com/intellij/execution/configurations/ConfigurationFactory.java) <!-- #L51-->和默认设置方法.
这些额外的覆盖是可选的.


## 运行配置


运行配置本身由[`RunConfiguration`](upsource:///platform/lang-api/src/com/intellij/execution/configurations/RunConfiguration.java)接口表示.
这里的“运行配置”是一些可以执行的命名配置文件,例如,
应用程序通过`main()`类,测试,远程调试启动到特定的机器/端口等.


以下是为特定项目定义的Java运行配置示例:


![运行配置](/basics/img/create-2.png)


实现运行配置时,您可能希望使用其中一个公共基类:


* [`RunConfigurationBase`](upsource:///platform/lang-api/src/com/intellij/execution/configurations/RunConfigurationBase.java)是一个通用超类,包含运行配置的最基本实现.

* [`LocatableConfigurationBase`](upsource:///platform/lang-api/src/com/intellij/execution/configurations/LocatableConfigurationBase.java)是一个公共基类,应该用于可以从上下文创建的配置
一个`RunConfigurationProducer`.
它支持从其设置自动生成配置名称,并跟踪用户是否更改了名称.

* [`ModuleBasedConfiguration`](upsource:///platform/lang-api/src/com/intellij/execution/configurations/ModuleBasedConfiguration.java)是与特定模块关联的配置的基类(例如, 
Java运行配置使用选定的模块来确定运行类路径.


## 设置编辑器


可以通过以下方式修改常见的运行配置设置:


[`RunConfiguration`特定的UI](upsource:///platform/lang-api/src/com/intellij/execution/configurations/RunConfiguration.java)<!-- #L48-->.
这由[`SettingsEditor`]处理(upsource:///platform/platform-api/src/com/intellij/openapi/options/SettingsEditor.java)<!-- #L97-->:


* [`getComponent()`](upsource:///platform/platform-api/src/com/intellij/openapi/options/SettingsEditor.java)<!-- #L97-->方法由IDE调用
显示运行配置特定UI.

* [`resetFrom()`](upsource:///platform/platform-api/src/com/intellij/openapi/options/SettingsEditor.java)<!-- #L83-->被调用以丢弃所有非
确认用户通过该UI进行的更改.

* [`applyTo()`](upsource:///platform/platform-api/src/com/intellij/openapi/options/SettingsEditor.java)调用<!--#L93-->确认更改,
即将当前UI状态复制到目标设置对象中.


## 持久性


该运行配置设置是持久的,即它们存储在文件系统中并在IDE启动时加载回来.
这是通过[`writeExternal()`]执行的(upsource:///platform/util/src/com/intellij/openapi/util/JDOMExternalizable.java)<!-- #L27-->和[`readExternal()
`](upsource:///platform/util/src/com/intellij/openapi/util/JDOMExternalizable.java)<!--#L26--> [RunConfiguration`]的方法(upsource:///platform/
lang-api/src/com/intellij/execution/configurations/RunConfiguration.java)相应的类.


 *IntelliJ Platform* 存储的实际配置由[`RunnerAndConfigurationSettings`](upsource:///platform/lang-api/src/com/intellij/execution/RunnerAndConfigurationSettings.java)类的实例表示,该类结合了
使用特定于运行程序的设置运行配置,以及跟踪某些运行配置标志,例如“临时”或“单例”.


当您需要从代码创建运行配置时,需要处理此类的实例.
这是通过以下两个步骤完成的:


*`RunManager.createConfiguration()`创建一个`RunnerAndConfigurationSettings`的实例.

*`RunManager.addConfiguration()`通过将其添加到项目中存储的共享配置列表或工作空间文件中存储的本地配置列表来使其持久化.


## 重构支持


大多数运行配置包含对其设置中的类,文件或目录的引用,并且通常需要在重命名或移动相应元素时更新这些设置.


为了支持这一点,您的运行配置需要实现[`RefactoringListenerProvider`](upsource:///platform/lang-api/src/com/intellij/execution/configurations/RefactoringListenerProvider.java)接口.


在你的`getRefactoringElementListener()`的实现中,你需要检查被重构的元素是否是你的运行配置所引用的元素,如果是,你返回一个[`RefactoringElementListener`](upsource:///platform/lang-api/src/com/intellij/refactoring/listeners/RefactoringElementListener.java)根据元素的新名称和位置更新配置.


## 从上下文创建配置


许多插件支持从上下文自动创建运行配置,以便用户可以单击(例如)应用程序或测试类,并使用正确的运行配置类型自动运行它.
为了支持这一点,您需要提供[`RunConfigurationProducer`]的实现(upsource:///platform/lang-api/src/com/intellij/execution/actions/RunConfigurationProducer.java)

接口并在``plugin.xml`中将其注册为`<runConfigurationProducer>`. 
(请注意,此API已在IntelliJ IDEA 13中重新设计;旧的[`RuntimeConfigurationProducer`](upsource:///platform/lang-api/src/com/intellij/execution/junit/RuntimeConfigurationProducer.java)是一个更多
混淆版本的相同API).


您需要实现的两个主要方法是:


*`setupConfigurationFromContext`接收您的类型的空白配置和`ConfigurationContext`,其中包含有关源代码位置的信息(可通过调用`getLocation()`或`getPsiLocation()`来访问).
您的实现需要检查该位置是否适用于您的配置类型(例如,如果它位于您支持的语言的文件中).
如果没有,则需要返回false,如果是,则需要将正确的特定于上下文的设置放入运行配置中并返回true.

*`isConfigurationFromContext`检查您的类型的指定配置是否是从指定的上下文创建的.
通过实现此方法,您可以重用现有的运行配置,该配置适用于当前上下文,而不是创建新的配置,并可能忽略用户在现有环境中执行的自定义.


请注意,为了支持从上下文创建的配置的自动命名,您的配置应该使用

[`LocatableConfigurationBase`](upsource:///platform/lang-api/src/com/intellij/execution/configurations/LocatableConfigurationBase.java)作为基类.


