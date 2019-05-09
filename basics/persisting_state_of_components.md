---
title: Persisting State of Components
---

* IntelliJ Platform *提供了一个API,允许组件或服务在重新启动IDE之间保持其状态.
您可以使用简单的API来保留一些值,也可以使用[PersistentStateComponent](upsource:///platform/core-api/src/com/intellij/openapi/components/PersistentStateComponent)来保持更复杂组件的状态. 
java)接口.


> **警告**如果您需要保留密码等敏感数据,请参阅[持久敏感数据](persisting_sensitive_data.md).


##使用PropertiesComponent进行简单的非可扩展持久化


如果您的插件唯一需要保留的是一些简单的值,最简单的方法是使用[`com.intellij.ide.util.PropertiesComponent`](upsource:///platform/core-api/src/com/intellij/ide/util/PropertiesComponent.java)服务.
它可用于保存应用程序级值和项目级值(存储在工作区文件中).
对于`PropertiesComponent`禁用漫游,因此仅将其用于临时的,不可漫游的属性.


使用`PropertiesComponent.getInstance()`方法存储应用程序级值,使用`PropertiesComponent.getInstance(Project)`方法存储项目级值.


由于所有插件共享相同的命名空间,因此强烈建议为键名称添加前缀(例如,使用插件ID).


##使用PersistentStateComponent


[`com.intellij.openapi.components.PersistentStateComponent`](upsource:///platform/projectModel-api/src/com/intellij/openapi/components/PersistentStateComponent.java)界面为您提供了定义值的最大灵活性
要坚持,他们的格式和存储位置.


要使用它:

 - 将[service](plugin_structure/plugin_services.md)或[component](plugin_structure/plugin_components.md)标记为实现`PersistentStateComponent`接口

 - 定义州级

 - 使用`@ com.intellij.openapi.components.State`指定存储位置


请注意,扩展实例不能通过实现`PersistentStateComponent`来保持其状态.
如果您的扩展需要具有持久状态,则需要定义一个负责管理该状态的单独服务.


###实现PersistentStateComponent接口


“PersistentStateComponent”的实现需要使用状态类的类型进行参数化.
状态类可以是单独的JavaBean类,也可以是实现`PersistentStateComponent`本身的类.


在前一种情况下,状态类的实例通常存储为`PersistentStateComponent`类中的字段:


```java
@State(...)
class MyService implements PersistentStateComponent<MyService.State> {
  
  static class State {
    public String value;
  }

  State myState;

  public State getState() {
    return myState;
  }

  public void loadState(State state) {
    myState = state;
  }
}
```

在后一种情况下,您可以使用以下模式来实现`getState()`和`loadState()`方法:


```java
@State(...)
class MyService implements PersistentStateComponent<MyService> {
  public String stateValue;

  public MyService getState() {
    return this;
  }

  public void loadState(MyService state) {
    XmlSerializerUtil.copyBean(state, this);
  }
}
```

###实现状态类


“PersistentStateComponent”的实现通过将公共字段,[annotated](upsource:///platform/util/src/com/intellij/util/xmlb/annotations)私有字段和bean属性序列化为XML格式来实现.


可以保留以下类型的值:

*数字(两种原始类型,如`int`和盒装类型,如`Integer`)

*布尔

*字符串

*收藏品

*地图

*枚举


要从序列化中排除公共字段或bean属性,请使用`@ com.intellij.util.xmlb.annotations.Transient`注释字段或getter.


请注意,状态类必须具有默认构造函数.
它应该返回组件的默认状态(如果XML文件中没有任何内容,则使用该状态).


State类应该有一个`equals`方法,但如果没有实现,状态对象将按字段进行比较.
使用Kotlin时,请使用[数据类](https://kotlinlang.org/docs/reference/data-classes.html).


###定义存储位置


要指定存储的确切位置,需要在`PersistentStateComponent`类中添加一个`@ State`注释.


它有以下字段:

*`name`(必需) - 指定状态的名称(XML中根标记的名称).

*`storages`  - 一个或多个`@ com.intellij.openapi.components.Storage`注释,用于指定存储位置.
项目级值的可选项 - 在这种情况下将使用标准项目文件.

*`reloadable`(可选) - 如果设置为false,则在外部更改XML文件并且状态已更改时,需要完成项目(或应用程序)重新加载.


指定`@ Storage`注释的最简单方法如下(自2016.x起;对于以前的版本,请参阅[old version](https://github.com/JetBrains/intellij-sdk-docs/blob/5dcb02991cf828a7d4680d125ce56b4c10234146/basics/persisting_state_of_components.md):


*`@Storage(“yourName.xml”)`如果组件是项目级的 - 对于基于`.ipr`的项目,将自动使用标准项目文件;
你不需要指定任何东西.


*`@Storage(StoragePathMacros.WORKSPACE_FILE)`用于存储在工作区文件中的值.


通过为`value`参数指定不同的值(2016.x之前的`file`),状态将保存在不同的文件中.


> **注意**对于应用程序级组件,强烈建议使用自定义文件,不推荐使用`other.xml`.


`@Storage`注释的`roamingType`参数指定使用Settings Repository插件时的漫游类型.


##自定义持久值的XML格式


请考虑仅使用注释参数来实现向后兼容性.
否则随意提交有关序列化妆品的问题.


如果要使用默认的bean序列化但需要自定义XML中的存储格式(例如,为了兼容以前版本的插件或外部定义的XML格式),可以使用`@Tag`,`@ Attribute` 
,`@ Property`,`@ MapAnnotation`,`@ AbstractCollection`注释.


有关更多信息,请参阅`com.intellij.util.xmlb.annotations`的`package.html`.


如果需要序列化的状态不能完全映射到JavaBean,则可以使用`org.jdom.Element`作为状态类.
在这种情况下,您可以使用`getState()`方法构建具有任意结构的XML元素,然后将其直接保存在状态XML文件中.
在`loadState()`方法中,您可以使用任何自定义逻辑反序列化JDOM元素树.
请注意,不建议这样做,应尽可能避免.


##持久性组件生命周期


在创建组件之后调用`loadState()`方法(仅当组件持有一些非默认状态时),并且在外部更改具有持久状态的XML文件后(例如,如果项目)
文件已从版本控制系统更新).
在后一种情况下,组件负责根据改变的状态更新UI和其他相关组件.


每次保存设置时都会调用`getState()`方法(例如,在帧停用或关闭IDE时).
如果从`getState()`返回的状态等于默认状态(通过使用默认构造函数创建状态类获得),则XML中不会保留任何内容.
否则,返回的状态将以​​XML格式化并存储.


## Legacy API(JDOMExternalizable)


较旧的组件使用[`JDOMExternalizable`](upsource:///platform/util/src/com/intellij/openapi/util/JDOMExternalizable.java)接口来保持状态.
它使用`readExternal()`方法从JDOM元素中读取状态,使用`writeExternal()`将状态写入它.


实现可以手动将状态存储在属性和子元素中,或使用[`DefaultJDOMExternalizer`](upsource:///platform/util/src/com/intellij/openapi/util/DefaultJDOMExternalizer.java)类来存储值
自动的所有公共领域.


组件将其状态保存在以下文件中:


*项目级:项目(`.ipr`)文件.
但是,如果`plugin.xml`文件中的workspace选项设置为`true`,则将使用workspace(`.iws`)文件.

*模块级:模块(`.iml`)文件.


