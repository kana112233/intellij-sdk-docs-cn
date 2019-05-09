---
title: Tool Windows
---

##工具Windows


_Tool windows_是用于显示信息的IDE的子窗口。
这些窗口通常在主窗口的外边缘有自己的工具栏(称为_tool窗口栏_)，其中包含一个或多个_tool窗口按钮_，它们激活显示在主IDE窗口左侧，底部和右侧的面板。
有关工具窗口的详细信息，请参阅[IntelliJ IDEA Web帮助](https://www.jetbrains.com/idea/help/tool-windows.html)。


每一侧包含两个工具窗口组，主要和次要工具窗口组，每个组中只有一个工具窗口可以一次处于活动状态。


每个工具窗口都可以显示多个选项卡(或“内容”，因为它们在API中调用)。

例如，“运行”工具窗口显示每个活动运行配置的选项卡，“更改”工具窗口显示一组固定的选项卡，具体取决于项目中使用的版本控制系统。


在插件中使用工具窗口有两种主要方案。

在第一个场景中(例如，Ant和Commander插件使用)，工具窗口按钮始终可见，用户可以随时激活它并与插件功能交互。

在第二个场景中(例如，“Analyze Dependencies”操作使用)，创建工具窗口以显示特定操作的结果，并且可以在操作完成后由用户关闭。


在第一个场景中，工具窗口使用`<toolWindow>`扩展点在* plugin.xml *中注册。

扩展点属性指定显示工具窗口按钮所需的所有数据:


*工具窗口的`id`(对应于工具窗口按钮上显示的文本)


*`anchor`，表示显示工具窗口的屏幕一侧(“左”，“右”或“底”)


*“secondary”属性，指定工具窗口是显示在主要组还是次要组中


*工具窗口按钮上显示的`icon`(13x13像素)


除此之外，您还可以指定* factory class *  - 实现该类的类的名称

[ToolWindowFactory](upsource:///platform/platform-api/src/com/intellij/openapi/wm/ToolWindowFactory.java)

接口。

当用户单击工具窗口按钮时，将调用工厂类的`createToolWindowContent()`方法，并初始化工具窗口的UI。

此过程可确保未使用的工具窗口不会导致启动时间或内存使用的任何开销:如果用户未与插件的工具窗口交互，则不会加载或执行插件代码。


如果不需要为所有项目显示插件的工具窗口，还可以指定* conditionClass *属性 - 实现该类的类的限定名称

[条件\ <项目\>](upsource:///platform/util-rt/src/com/intellij/openapi/util/Condition.java)

interface(这可以是与工具窗口工厂实现相同的类)。

如果条件返回“false”，则不会显示工具窗口。

请注意，在加载项目时仅评估条件一次;

如果您想在用户使用项目时动态显示和隐藏工具窗口，则需要使用第二种方法进行工具窗口注册。


第二种方法涉及简单地调用

[ToolWindowManager.registerToolWindow()](upsource:///platform/platform-api/src/com/intellij/openapi/wm/ToolWindowManager.java)

从你的插件代码。

该方法具有多个重载，可根据您的任务使用。

如果使用带有组件的重载，则组件将成为工具窗口中显示的第一个内容(选项卡)。


显示许多工具窗口的内容需要访问索引。

因此，工具窗口通常在构建索引时被禁用，除非您将`canWorkInDumbMode`的值传递给`registerToolWindow()`函数。


如前所述，工具窗口可以包含多个选项卡或内容。

要管理工具窗口的内容，您可以调用

[ToolWindow.getContentManager()](upsource:///platform/platform-api/src/com/intellij/openapi/wm/ToolWindow.java)。

要添加选项卡(内容)，首先需要通过调用来创建它

[ContentManager.getFactory().createContent()](upsource:///platform/platform-api/src/com/intellij/ui/content/ContentManager.java)，

然后使用将其添加到工具窗口

[ContentManager.addContent()](upsource:///platform/platform-api/src/com/intellij/ui/content/ContentManager.java)。


您可以控制是否允许用户全局或基于每个选项卡关闭选项卡。

前者是通过将`canCloseContents`参数传递给`registerToolWindow()`函数，或者通过指定

* canClose中的`canCloseContents =“true”`*。
默认值为“false”;
除非明确设置`canCloseContents`，否则将忽略对ContentManager内容调用setClosable(true)。

如果通常启用了关闭选项卡，则可以通过调用禁用特定选项卡的关闭

[Content.setCloseable(假)](upsource:///platform/platform-api/src/com/intellij/ui/content/Content.java)。


##如何创建工具窗口？


IntelliJ平台提供_toolWindow_ [扩展点](/basics/plugin_structure/plugin_extensions_and_extension_points.md)，您可以使用它来创建和配置自定义工具窗口。
使用[ToolWindowEP](upsource:///platform/platform-api/src/com/intellij/openapi/wm/ToolWindowEP.java)bean类声明此扩展点。


要创建工具窗口，首先声明_toolWindow_扩展点的扩展名。


###创建插件


要创建显示自定义工具窗口的插件，请执行以下步骤:


1.在插件项目中，创建一个实现[ToolWindowFactory](upsource:///platform/platform-api/src/com/intellij/openapi/wm/ToolWindowFactory.java)接口的Java类。

2.在这个类中，重写`createToolWindowContent`方法。
此方法指定工具窗口的内容。

3.在插件配置文件plugin.xml中，创建`<extensions defaultExtensionNs =“com.intellij”> ... </extensions>`部分。

4.在本节中，添加`<toolWindow>`元素，对于此元素，设置ToolWindowEP bean类中声明的以下属性:
    
 -  ** id **(必填):指定工具窗口标题。
    
 -  ** anchor **(必需):指定将显示工具窗口按钮的工具窗口栏。
可能的值:“left”，“right”，“top”，“bottom”。
    
 -  ** secondary **(可选):当为true时，工具窗口按钮将显示在工具窗口栏的下部。
默认值为false。
    
 -  ** factoryClass **(必需):指定实现ToolWindowFactory接口的Java类(参见步骤1)。
    
 -  ** icon **(可选):指定标识工具窗口的图标的路径(如果有)。
    
 -  ** conditionClass **(可选):指定实现[Condition](upsource:///platform/util-rt/src/com/intellij/openapi/util/Condition.java)接口的Java类。
使用此类，您可以定义要显示工具窗口按钮的条件。
在Condition类中，您应该覆盖value方法:如果此方法返回false，则工具窗口栏上不会显示工具窗口按钮。


为了阐明上述过程，请考虑以下`plugin.xml`文件的片段:


```xml
<extensions defaultExtensionNs="com.intellij">
    <toolWindow id="My Sample Tool Window" icon="/myPackage/icon.png" anchor="right" factoryClass="myPackage.MyToolWindowFactory"/>
</extensions>
```

###示例插件


为了阐明如何开发创建工具窗口的插件，请考虑[code_samples]中提供的** toolWindow **示例插件(https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/) 
SDK文档的目录。
此插件创建** Sample Calendar **工具窗口，显示系统日期，时间和时区。


**运行toolWindow插件**


1. Start **IntelliJ IDEA** and open the **tool_window** project saved into the [code_samples/tool_window](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/tool_window) directory.
2. Ensure that the project settings are valid for your environment. If necessary, modify the project settings.
To view or modify the project settings, you can open the [Project Structure](https://www.jetbrains.com/help/idea/project-structure-dialog.html) dialog.
3. Run the plugin by choosing the **Run | Run** on the main menu.
If necessary, change the [Run/Debug Configurations](https://www.jetbrains.com/help/idea/run-debug-configuration-plugin.html).

该插件创建** Sample Calendar **工具窗口。
打开时，此工具窗口类似于以下屏幕:


![样本日历](img/sample_calendar.png)


