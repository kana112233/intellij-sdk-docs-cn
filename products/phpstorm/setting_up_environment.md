---
title: Setting-up the Environment for PhpStorm Plugin Development
redirect_from:
  - /phpstorm/setting_up_environment.html
---

### 一般信息

请熟悉[插件开发入门](/basics/getting_started.md)

本指南的一节.


如果您使用的是[DevKit](/basics/getting_started/using_dev_kit.md)

为PhpStorm开发插件的工作流程,推荐的方法是将插件项目SDK基于安装

PhpStorm.
另一种方法是使用PHP插件将插件项目SDK基于IntelliJ IDEA Ultimate的安装.

但是,这会冒着意外使用PhpStorm中没有的某些API的风险.


###如何使用PhpStorm OpenAPI库进行插件开发


> **注意** OpenAPI可用于PhpStorm 6及更高版本.


[Plugin Dependencies](/basics/plugin_structure/plugin_dependencies.md)页面描述了向依赖项添加依赖项

DevKit以及Gradle开发环境.
按照说明:


*将PHP OpenAPI类添加到插件的类路径中:
  
*对于基于Gradle的插件开发,请将`com.jetbrains.php`插件ID添加到build.gradle文件中.
  
*对于基于DevKit的插件开发,将`php-openapi.jar`和`php.jar`库添加到项目SDK的类路径中.
    
这些库位于`<your_installation_of_PhpStorm>/plugins/php/lib`目录中.


*添加`com.jetbrains.php`和`com.intellij.modules.platform`依赖项
  
到你的插件项目的'plugin.xml`文件.


