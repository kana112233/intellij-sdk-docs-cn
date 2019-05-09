---
title: Deploying a Plugin
---

在使用自定义插件之前,必须先部署它:构建,安装,然后使用插件管理器启用.


要部署插件:


* Make your project by invoking **Build \| Build Project** or **Build \| Build Module \<module name\>**.
* Prepare your plugin for deployment. In the main menu, select **Build \| Prepare Plugin Module \<module name\> for Deployment**.

![准备部署插件](deploying_plugin/img/prepare_plugin_for_deployment.png)


*如果插件模块不依赖于任何库,则会创建`.jar`存档.
否则,将创建一个`.zip`存档,包括项目设置中指定的所有插件库.


![罐子保存通知](deploying_plugin/img/jar_saved_notification.png)


* [安装](https://www.jetbrains.com/help/idea/managing-plugins.html#installing-plugins-from-disk)
  
磁盘中新创建的archive/jar文件. 
`editor_basics`代码示例将插件archive/jar构建到`editor_basics`项目文件夹中:


![Jar文件位置](deploying_plugin/img/jar_location.png)


*重新启动IDE,以使更改生效.


