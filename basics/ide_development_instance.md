---
title: IDE Development Instances - Settings, Caches, Logs, and Plugins
redirect_from:
  - /basics/settings_caches_logs.html
---

用于开发插件的JetBrains功能是从JetBrains IDE中运行或调试插件项目,例如: 
IntelliJ IDEA.

[选择** Run **菜单](https://www.jetbrains.com/help/idea/running-and-debugging-plugins.html)将启动

启用了插件的IDE的_Development Instance_.


** Sandbox Home **目录包含

[设置,缓存,日志和插件](#development-instance-settings-caches-logs-and-plugins)

用于IDE的开发实例.
此信息存储在与其不同的位置

[installed IDE itself](https://intellij-support.jetbrains.com/hc/en-us/articles/206544519-Directories-used-by-the-IDE-to-store-settings-caches-plugins-and-logs).


###基于Gradle的插件项目的Sandbox Home位置

对于基于Gradle的插件,默认的** Sandbox Home **位置由IntelliJ平台`gradle-intellij-plugin`定义.

请参阅[配置Gradle插件项目](/tutorials/build_system/prerequisites.md)

有关指定** Sandbox Home **位置的详细信息.
默认** Sandbox Home **位置

对于基于Gradle的插件项目是:

* ** Windows **`<Project Dir> \ build \ idea-sandbox`

* ** Linux或macOS **`<Project Dir>/build/idea-sandbox`


###基于DevKit的插件项目的沙箱主页位置

对于基于DevKit的插件,默认的** Sandbox Home **位置在IntelliJ Platform Plugin SDK中定义.

有关详细信息,请参阅指定[Sandbox Home for DevKit Projects](/basics/getting_started/setting_up_environment.md).

基于DevKit的插件项目的默认** Sandbox Home **目录位置是:

* ** Windows:**`<用户主页> \.<product_system_name> <product_version> \ system \ plugins-sandbox \`

* ** Linux:**`〜/.<product_system_name> <product_version>/system/plugins-sandbox/`

* ** macOS **`〜/Library/Caches/<product_system_name> <product_version>/plugins-sandbox/`


###开发实例设置,缓存,日志和插件

在** Sandbox Home **目录中有与开发实例有关的子目录:

*`config`包含IDE实例的设置.

*`plugins`包含在IDE实例中运行的每个插件的文件夹.

*`system/caches`或`system \ _ caches`包含IDE实例数据.

*`system/log`或`system \ log`包含IDE实例的`idea.log`文件.


可以手动清除这些** Sandbox Home **子目录中的每一个以重置IDE开发实例.

在下一次启动开发实例时,将使用适当的信息重新填充子目录.


