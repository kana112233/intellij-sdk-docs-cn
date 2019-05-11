---
title: Main Types of Plugins
---

基于 *IntelliJ Platform* 的产品可以通过添加插件进行修改和调整以用于自定义目的.
所有可下载的插件都可以在[JetBrains Plugin Repository](https://plugins.jetbrains.com/)上找到.


最常见的插件类型包括:
* 自定义语言支持
* 框架集成
* 工具集成
* 用户界面附加组件

## 自定义语言支持


自定义语言支持提供了使用特定编程语言的基本功能.
这包括:

* 文件类型识别
* 词法分析
* 语法突出显示
* 格式化
* 代码洞察和代码完成
* 检查和快速修复
* 意图行动

请参阅[自定义语言支持教程](/tutorials/custom_language_support_tutorial.md)以了解有关该主题的更多信息.

## 框架集成

框架集成包括改进的代码洞察功能,这些功能对于给定的框架是典型的,以及直接从IDE使用框架特定功能的选项.
有时它还包括自定义语法或DSL的语言支持元素.

* 具体的代码洞察力
* 直接访问特定于框架的功能

请参阅[Struts 2插件](https://plugins.jetbrains.com/plugin/1698)作为框架集成的示例.

## 工具集成

通过工具集成,可以直接从IDE操作第三方工具和组件,而无需切换上下文.

这意味着:

* 实施其他行动
* 相关的UI组件
* 访问外部资源

请参阅[Gerrit集成](https://plugins.jetbrains.com/plugin/7272?pr=idea)插件作为示例.

## 用户界面附加组件

此类别中的插件会对IDE的标准用户界面应用各种更改.
一些新添加的组件是交互式的并提供新功能,而其他组件仅限于视觉修改. 
[背景图片](https://plugins.jetbrains.com/plugin/72)插件可以作为一个例子.


