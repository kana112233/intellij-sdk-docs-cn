---
title: Plugin Services
---

当你的插件调用[ServiceManager](upsource:///platform/core-api/src/com/intellij/openapi/components/ServiceManager.java)类的`getService`方法时,_service_是一个按需加载的插件组件
.


* IntelliJ Platform *确保即使多次调用服务,也只加载一个服务实例.
服务必须具有用于服务实例化的实现类.
服务还可以具有接口类,该接口类用于获取服务实例并提供服务的API.
接口和实现类在`plugin.xml`文件中指定.


* IntelliJ Platform *提供三种类型的服务:应用程序级服务,项目级服务和_module level_服务.


##如何申报服务？


要声明服务,可以在IntelliJ平台中使用以下扩展点:


*`applicationService`:用于声明应用程序级服务.

*`projectService`:旨在声明项目级服务.

*`moduleService`:用于声明模块级服务.


**申报服务:**


1.在项目中,打开目标包的上下文菜单,然后单击*新建*(或按<kbd> Alt </kbd> + <kbd>插入</kbd>).

2.在* New *菜单中,选择* Plugin DevKit *并单击* Application Service *,* Project Service *或* Module Service *,具体取决于您需要使用的服务类型.

3.在打开的对话框中,您可以指定服务接口和实现,如果取消选中*将实现*与实例*分隔,则只需指定服务类.


IDE将生成新的Java接口和类(或者如果取消选中则只是一个类*从实现*中分离接口复选框)并在`plugin.xml`文件中注册新服务.


> **注意**自版本** 2017.3 **起,可通过* New *上下文菜单声明服务.


为了阐明服务声明过程,请考虑以下`plugin.xml`文件的片段:


```xml
<extensions defaultExtensionNs="com.intellij">
  <!-- Declare the application level service -->
  <applicationService serviceInterface="Mypackage.MyApplicationService" 
                      serviceImplementation="Mypackage.MyApplicationServiceImpl" />

  <!-- Declare the project level service -->
  <projectService serviceInterface="Mypackage.MyProjectService" 
                  serviceImplementation="Mypackage.MyProjectServiceImpl" />
</extensions>
```

如果没有指定`serviceInterface`,它应该与`serviceImplementation`具有相同的值.


##检索服务


要在Java代码中实例化您的服务,请使用以下语法:


```java
MyApplicationService applicationService = ServiceManager.getService(MyApplicationService.class);

MyProjectService projectService = ServiceManager.getService(project, MyProjectService.class);

MyModuleService moduleService = ModuleServiceManager.getService(module, MyModuleService.class);
```

###示例插件


本部分允许您下载和安装示例插件,说明如何创建和使用插件服务.
该插件有一个项目组件,它实现了一个服务,该服务计算IDE中当前打开的项目的数量.
如果此数字超过允许的最大同时打开项目数,则插件将返回错误消息并关闭最近打开的项目.


<! -  TODO在可用时替换为其他插件URL  - >


**安装并运行示例插件**


* Download the included sample plugin project located [here](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/max_opened_projects).
* Start *IntelliJ IDEA*, on the starting page, click *Open Project*, and then use the *Open Project* dialog box to open the project *max_opened_projects*.
* On the main menu, choose *Run \| Run* or press <kbd>Shift</kbd>+<kbd>F10</kbd>.
* If necessary, change the [Run/Debug Configurations](https://www.jetbrains.com/help/idea/run-debug-configuration-plugin.html).


