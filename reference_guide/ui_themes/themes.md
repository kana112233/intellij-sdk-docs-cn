---
title: Creating Custom UI Themes
---

创建自定义UI主题是选择基本IDE主题(_Light_或_Darcula_)然后更改基本主题定义的各个方面的过程.

自定义UI主题可以:

*替换图标,

*更改图标和UI控件的颜色,

*改变UI控件的边框和插图,

*提供自定义编辑器方案,

*添加背景图片.


## UI主题的结构

UI主题是IntelliJ平台插件中的组件.

主题插件应该是独立的插件,而不是与其他插件功能相结合.

此方法是最佳用户体验,因为它可以避免在安装UI Theme插件时重新启动IDE.


UI主题有几个组成部分:

*插件项目的`resources`文件夹中的必需主题描述(JSON)文件.

*插件的`plugin.xml`文件中需要的`themeProvider`声明,位于插件项目的`META-INF`文件夹中.

*从导出的IDE编辑器方案派生的可选编辑器方案描述(XML)文件.
  
该文件位于插件项目的`resources`文件夹中.

*一个可选的背景图像文件,位于插件项目的`resources`文件夹中.

*可选的图标图像文件,位于插件项目的`resources`文件夹中.


![UI主题组件](img/theme_components.png)


##自定义UI主题工作流程

创建UI主题遵循以下一般顺序:

* [从插件项目开始](/basics/getting_started/creating_plugin_project.md)

* [使用DevKit UI主题向导将UI主题组件添加到插件](#creating-a-ui-theme-with-the-devkit-theme-wizard).

* [自定义UI主题](themes_customize.md)通过将数据对象添加到主题描述(JSON)文件.

* [添加编辑器方案或背景图像](themes_extras.md)到插件.

* [构建和测试](/basics/getting_started/running_and_debugging_a_plugin.md)UI主题插件.

* [部署UI主题插件](/basics/getting_started/deploying_plugin.md)

* [发布UI主题插件](/basics/getting_started/publishing_plugin.md)


##创建自定义UI主题


使用DevKit UI主题向导将UI主题添加到IntelliJ平台插件.


###使用DevKit主题向导创建UI主题

DevKit向导是DevKit插件的一部分,该插件与IntelliJ IDEA捆绑在一起.

此向导可用于基于DevKit和基于Gradle的插件.


While a plugin project is open in IntelliJ IDEA, select the `resources` folder in the _Project_ tool window.
From the main menu, select the _**New | Plugin DevKit | Theme**_ action. 

![DevKit Wizard Action](img/devkit_wiz_action.png)


点击


然后,向导会提示输入新主题的名称,以及主题的基础:


![DevKit Dialog](img/devkit_wiz_dialog.png)


最佳做法是将新主题命名为与插件名称相同.

该复选框表示主题的基础.

检查_Dark theme_意味着在_Darcula_上创建自定义主题.

保持框未选中意味着将自定义主题基于默认的IntelliJ IDEA _Light_主题.

对于SDK代码示例`theme_basics`,该框是_unchecked_.


Clicking the _OK_ button creates a default Theme description file named `[themeName].theme.json` in the plugin project's `resources` folder. 
In this example, the file is named `theme_basics.theme.json`. 
The content of the default file is a short set of key–value pairs:  
```json
{
 "name": "theme_basics",
 "dark": false,
 "author": "",
 "editorScheme": "/theme_basics.xml",
 "ui": {
 }
}
```

`name`键的`value`匹配主题描述`[themeName] .theme.json`文件名的第一部分.

当UI主题的插件安装在IDE中时,`name`的`value`显示在[Theme](https://www.jetbrains.com/help/idea/settings-appearance.html)_Preferences_下拉列表中.

`author`键的值默认为空.

`editorScheme`部分将在[添加自定义编辑器方案]中解决(themes_extras.md#adding-a-custom-editor-scheme)

`ui`部分将在[Customizing UI Control Colors](themes_customize.md#customizing-ui-controls)中解决.


The Wizard also creates a `themeProvider` declaration in the `<extensions>` section of the plugin's `plugin.xml` file. 
This declaration binds the Theme description file to a theme provider extension using a generated unique `id`.
```xml
  <extensions defaultExtensionNs="com.intellij">
    <themeProvider id="eb9b7461-397b-4b98-a422-224fc0a74564" path="/theme_basics.theme.json"/>
  </extensions>
```
> **WARNING** Do not modify or re-use an existing value of the generated `id` attribute.

此时,UI主题`theme_basics`是一个有效的UI主题.

它的插件可以在基于IntelliJ平台的IDE中构建和测试,让用户有机会在[主题](https://www.jetbrains.com/help/idea/settings-appearance.html)_Preferences_下拉列表中选择_theme_basics_ 
.

自定义主题看起来与IntelliJ IDEA默认的`Light`主题完全相同,但它是一个有效的自定义主题.


###完成默认UI主题描述文件

如果需要,可以直接编辑默认UI主题定义以添加或更改默认键的某些值:

可以在Theme(`* .theme.json`)描述文件中直接更改以下值:

*`dark`键的值可以改为`true`,这会将主题的基础切换为_Darcula_而不是_Light_.

*`author`键的值,默认为空字符串,可以设置为`String`字面值.

在`theme_basics`代码示例的情况下,它被设置为“IntelliJ Platform SDK”.


