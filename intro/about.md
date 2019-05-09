---
title: About this Guide
---

本指南分为几个部分,类似于教科书.
每个部分都基于前一部分的内容,但没有必要按顺序阅读指南. 
[Key Topics](key_topics.md)页面旨在链接到能够理解体系结构并开始构建插件所必需的页面.


> **注意**浏览本指南时,您会注意到有些主题是灰色的.
遗憾的是,该指南并不完整,并包含某些主题的占位符.
我们正在努力提高覆盖率,但如果因内容缺失而陷入困境,请参阅[获得帮助](getting_help.md)部分,了解有关如何再次移动的详细信息.

>

>该指南也是[GitHub上的开源](https://github.com/JetBrains/intellij-sdk-docs),我们非常感谢收到新内容或更新的请求.
拉取请求不需要完全全面 - 如果稍微更新对您有帮助,它也将帮助其他开发人员!
所有拉取请求将在被接受之前进行审核,因此不必担心不准确.
有关在本地构建指南并做出贡献的详细信息,请参阅[贡献](/CONTRIBUTING.md)页面.


* [**第一部分 - 插件**](/basics.md)


描述如何创建可以扩展_IntelliJ Platform_的插件.
包括有关如何设置项目,注册扩展点,确定_IntelliJ Platform_的特定版本以及如何打包,部署和测试插件的详细信息.


* [**第二部分 - 基础平台**](/platform/fundamentals.md)


描述了体系结构的基础层,它提供了许多功能和实用程序,例如组件模型,用户界面,文档和编辑器,虚拟文件​​系统,设置和线程以及后台任务.
基础平台层基本上包含_IntelliJ Platform_的功能,该功能不针对语言功能或解析.


* [**第III部分 - 项目模型**](/basics/project_structure.md)


记录项目模型,它表示当前加载的项目的文件和配置,以及用于构建项目的构建系统.


* [**第四部分 -  PSI **](/basics/architectural_overview/psi.md)


程序结构接口为许多不同的文件类型构建语法和语义模型.
本节介绍如何使用PSI,导航和操作语法树,还介绍了强大的引用系统,它允许语法树节点引用语义模型中的项.
它还详细说明了PSI如何创建和使用索引.


* **第五部分 - 特点**


描述如何扩展和与使用PSI层的各种功能进行交互,例如代码完成,导航,<kbd> Alt </kbd> + <kbd>输入</kbd>项目,意图,重构等.
有关特定于语言的功能,请参阅下面的自定义语言部分,该功能仅在添加对新语言的支持时适用.


* [**第VI部分 - 测试**](/basics/testing_plugins.md)


描述用于编写涵盖插件功能的自动化测试的可用基础结构.


* [**第VII部分 - 自定义语言**](/reference_guide/custom_language_support.md)


插件经常扩展对现有语言的支持,例如向Java文件添加检查.
本节介绍如何为_IntelliJ Platform_添加对新语言的支持,默认情况下不支持该语言,创建解析器,语法和语义模型以及构建在顶层的所有功能.


* [**第VIII部分 - 产品特定**](/products/idea.md)


_IntelliJ Platform_中的许多功能都是语言和产品无关的.
例如,代码检查在Java中的工作方式与在Ruby中的工作方式相同,只是语法树和语义信息不同.
本节介绍产品特定功能,例如特定项目模型差异以及如何在插件中定位它们.


* **第IX部分 - 自定义IDE **


记录如何使用_IntelliJ Platform_创建新的自定义IDE,而不是插入现有产品,例如
像WebStorm或Android Studio.


* [** Part X  - 插件存储库API **](/plugin_repository/index.md)


记录JetBrains维护的[Plugin Repository](https://plugins.jetbrains.com)服务的API,用于托管插件.
没有必要知道这个API才能发布插件 - 插件可以手动上传,也可以通过Gradle IntelliJ插件上传.


* [**附录I  - 资源**](/appendix/resources/useful_links.md)


链接到有用的资源,例如IntelliJ Community Edition源代码,插件开发论坛和插件开发者Gitter室.


* [**附录II  - 打破API变更**](/reference_guide/api_changes_list.md)


提供IntelliJ平台每个主要版本中向后不兼容的API更改列表.


