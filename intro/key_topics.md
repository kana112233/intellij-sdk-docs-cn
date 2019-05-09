---
title: Key Topics
---

_IntelliJ Platform_非常庞大,非常强大,其规模和范围最初可能非常艰巨.
此页面旨在列出插件作者感兴趣的关键主题,并提供指向最常见扩展点的快速链接.


##基本概念


 - 带插件的[入门](/basics/getting_started.md).

 -  [测试插件](/basics/testing_plugins.md).

 -  [建筑概述]  - 简要介绍_IntelliJ Platform_的不同层次.

 - 组件模型 -  _IntelliJ Platform_是一个基于组件的应用程序,负责创建组件和注入依赖项.
理解这一点对于构建插件是必要的.

 - 扩展点 - 如何使用扩展点注册组件,以及如何找出可用的扩展点.

 -  [虚拟文件](/basics/architectural_overview/virtual_file.md) - 所有文件访问都应通过虚拟文件系统来抽象和缓存文件系统.
这意味着您可以使用本地文件系统上的文件,zip文件或版本控制中的旧版本.

 -  [扩展点](/basics/plugin_structure/plugin_extensions_and_extension_points.md)


##代码模型


_IntelliJ Platform_的代码模型称为PSI  - 程序结构接口. 
PSI解析代码,构建索引并创建语义模型.


##常用扩展点


_IntelliJ Platform_具有极强的可扩展性,可以扩展大多数功能和服务.
一些常见的扩展点是:


* [Actions](/tutorials/action_system.md) - 菜单和工具栏项

* [代码检查](/tutorials/code_inspections.md) - 代码分析,查看语法树和语义模型,并突出显示编辑器中的问题.

* [Intentions](/tutorials/code_intentions.md) - 当文本插入符位于某个位置时,<kbd> Alt </kbd> + <kbd> Enter </kbd>菜单中可用的上下文特定操作.

* [代码完成](/reference_guide/custom_language_support/code_completion.md).


