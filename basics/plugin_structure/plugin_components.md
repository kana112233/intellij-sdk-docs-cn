---
title: Plugin Components
---

组件是插件集成的基本概念.
有三种组件:


* **在IDE启动时创建并初始化应用程序级组件**.
可以使用`getComponent(Class)`方法从[Application](upsource:///platform/core-api/src/com/intellij/openapi/application/Application.java)实例中获取它们.

* **为IDE中的每个[`Project`](upsource:///platform/core-api/src/com/intellij/openapi/project/Project.java)实例创建项目级组件**. 
(请注意,即使未打开的项目也可以创建组件.)可以使用`getComponent(Class)`方法从`Project`实例获取它们.

* **在IDE中加载的每个项目中为每个[`Module`](upsource:///platform/core-api/src/com/intellij/openapi/module/Module.java)创建模块级组件** 
.

可以使用`getComponent(Class)`方法从`Module`实例获取模块级组件.


每个组件都应该具有配置文件中指定的接口和实现类.
接口类将用于从其他组件检索组件,实现类将用于组件实例化.


注意同一级别的两个组件([Application](upsource:///platform/core-api/src/com/intellij/openapi/application/Application.java),[Project](upsource:///platform/core-api/src/com/intellij/openapi/project/Project.java)或[Module](upsource:///platform/core-api/src/com/intellij/openapi/module/Module.java))不能
具有相同的接口类.
可以为接口和实现指定相同的类.


每个组件都有一个唯一的名称,用于其外部化和其他内部需求.
组件的名称由其`getComponentName()`方法返回.


## 组件命名表示法


建议以“<plugin_name>.<component_name>”的形式命名组件.


## 应用程序级组件


可选地,应用程序级组件的实现类可以实现[ApplicationComponent](upsource:///platform/core-api/src/com/intellij/openapi/components/ApplicationComponent.java)接口.


没有依赖项的应用程序组件应该有一个没有参数的构造函数,它将用于实例化.
如果应用程序组件依赖于其他应用程序组件,则它可以将这些组件指定为构造函数参数. 
 *IntelliJ Platform* 将确保以正确的顺序实例化组件以满足依赖性.


应用程序级组件必须在plugin.xml文件的`<application-components>`部分中注册(请参阅[插件配置文件](plugin_configuration_file.md)).


## 项目级组件


可选地,项目级组件的实现类可以实现[ProjectComponent](upsource:///platform/core-api/src/com/intellij/openapi/components/ProjectComponent.java)接口.


如果需要项目实例,项目级组件的构造函数可以具有[Project](upsource:///platform/core-api/src/com/intellij/openapi/project/Project.java)类型的参数. 
.
如果依赖于这些组件,它还可以将其他应用程序级或项目级组件指定为参数.


项目级组件必须在`plugin.xml`文件的`<project-components>`部分中注册(参见[Plugin Configuration File](plugin_configuration_file.md)).


## 模块级组件


可选地,模块级组件的实现类可以实现[ModuleComponent](upsource:///platform/projectModel-api/src/com/intellij/openapi/module/ModuleComponent.java)接口.


如果需要模块实例,则模块级组件的构造函数可以具有Module类型的参数.
如果它依赖于那些组件,它还可以将其他应用程序级别,项目级别或模块级别组件指定为参数.


模块级组件必须在`plugin.xml`文件的`<module-components>`部分中注册(参见[Plugin Configuration File](plugin_configuration_file.md)).


## 持久化组件的状态


如果组件的类实现[JDOMExternalizable](upsource:///platform/util/src/com/intellij/openapi/util/JDOMExternalizable.java)(不建议使用)或[PersistentStateComponent],则会自动保存和加载每个组件的状态. 
](upsource:///platform/projectModel-api/src/com/intellij/openapi/components/PersistentStateComponent.java)界面.


当组件的类实现[PersistentStateComponent](upsource:///platform/projectModel-api/src/com/intellij/openapi/components/PersistentStateComponent.java)接口时,组件状态将保存在您可以指定的XML文件中
使用[@State](upsource:///platform/projectModel-api/src/com/intellij/openapi/components/State.java)和[@Storage](upsource:///platform/projectModel-api/src/com/intellij/openapi/components/Storage.java)Java代码中的注释.


当组件的类实现[JDOMExternalizable](upsource:///platform/util/src/com/intellij/openapi/util/JDOMExternalizable.java)接口时,组件将其状态保存在以下文件中:


*项目级组件将其状态保存到项目(`.ipr`)文件中.
 
  
但是,如果`plugin.xml`文件中的workspace选项设置为`true`,则组件将其配置保存到工作空间(`.iws`)文件中.


*模块级组件将其状态保存到模块(`.iml`)文件中.


有关更多信息和示例,请参阅[持久性组件状态](/basics/persisting_state_of_components.md).


## 默认值


默认值(组件的预定义设置)应放在`<component_name> .xml`文件中.
将此文件放在与默认包对应的文件夹中的插件的类路径中. 
`readExternal()`方法将在`<component>`root标签上调用.


如果组件有默认值,则会调用`readExternal()`方法两次:


*第一次默认

*第二次保存配置


## 插件组件生命周期


组件按以下顺序加载:


* Creation  - 调用构造函数.

*初始化 - 调用`initComponent`方法(如果组件实现[ApplicationComponent](upsource:///platform/core-api/src/com/intellij/openapi/components/ApplicationComponent.java)接口).

*配置 - 调用`readExternal`方法(如果组件实现[JDOMExternalizable](upsource:///platform/util/src/com/intellij/openapi/util/JDOMExternalizable.java)接口)或`loadState`
调用方法(如果组件实现[PersistentStateComponent](upsource:///platform/projectModel-api/src/com/intellij/openapi/components/PersistentStateComponent.java)并且具有非默认持久化状态).

*对于模块组件,调用[ModuleComponent]的“moduleAdded”方法(upsource:///platform/projectModel-api/src/com/intellij/openapi/module/ModuleComponent.java)接口以通知模块具有
已添加到项目中.

*对于项目组件,调用[ProjectComponent](upsource:///platform/core-api/src/com/intellij/openapi/components/ProjectComponent.java)接口的`projectOpened`方法来通知项目有
已装载.


按以下顺序卸载组件:


*保存配置 - 调用`writeExternal`方法(如果组件实现[JDOMExternalizable](upsource:///platform/util/src/com/intellij/openapi/util/JDOMExternalizable.java)接口),或者`
调用getState`方法(如果组件实现PersistentStateComponent).

* Disposal  - 调用`disposeComponent`方法.


请注意,您不应该在组件的构造函数中使用`getComponent()`方法请求任何其他组件,否则您将得到一个断言.
如果在初始化组件时需要访问其他组件,则可以将它们指定为构造函数参数,或者在`initComponent`方法中访问它们.


