---
title: Creating a Plugin Project
---

> **注意**对于新项目,建议使用[Gradle](/tutorials/build_system.md).


本节介绍如何使用“新建项目”向导从头开始创建新的插件项目. 
(可选)您可以导入现有项目或从外部模型导入项目.
您还可以将新插件模块添加到现有的* IntelliJ Platform *项目中.

有关更多信息,请参阅[IntelliJ IDEA Web帮助](https://www.jetbrains.com/idea/help/new-project-wizard.html).


###创建IntelliJ平台插件项目:


*  On the main menu, choose **File \| New \| Project**. The *New Project*
   wizard starts.

![新建项目向导](img/new_project_wizard.png)


*  Set *IntelliJ Platform Plugin* project type
*  Click **Next**
*  Set desired project name
*  Click **Finish** to generate project structure files
*  Go to **File \| Project Structure** to customize project settings if required

### To Create an IntelliJ Platform Plugin Module
*  Select **File \| New \| Module** and choose the *IntelliJ Platform Plugin* module type

![IntelliJ平台插件模块](img/intellij_platform_plugin_module.png)

<BR/>

<BR/>

*输入您想要的插件名称.


*  Go to **File \| Project Structure** and select the newly created *IntelliJ Platform SDK* as the default SDK for the plugin module:

![设置插件模块SDK](img/set_plugin_module_sdk.png)


