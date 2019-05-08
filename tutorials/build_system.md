---
title: Building plugins with Gradle
---

[gradle-intellij-plugin](https://github.com/JetBrains/gradle-intellij-plugin)Gradle插件是构建IntelliJ插件的推荐解决方案。

该插件负责处理插件项目的依赖性 - 基本IDE和插件可能依赖的其他插件。
 

它还提供了使用插件运行IDE并将插件发布到[JetBrains插件存储库](/plugin_repository/index.md)的任务。

为了确保您的插件不受平台主要版本之间可能发生的[API更改](/reference_guide/api_changes_list.md)的影响，您可以轻松地针对基本IDE的许多版本构建插件。


以下教程是指可在包含的[gradle_plugin_demo](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/gradle_plugin_demo)项目中找到的材料。

以下是配置Gradle支持的一系列指南。

* [1。
入门](build_system/prerequisites.md)

* [2。
部署插件](build_system/deployment.md)


