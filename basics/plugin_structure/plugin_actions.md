---
title: Plugin Actions
---

* IntelliJ Platform *提供了_actions_的概念.
动作是一个类,派生自[`AnAction`](upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnAction.java)类,其`actionPerformed`方法是
选择菜单项或工具栏按钮时调用.


动作系统允许插件将自己的项目添加到IDEA菜单和工具栏.
操作被组织成组,而组又可以包含其他组.
一组动作可以形成工具栏或菜单.
该组的子组可以形成菜单的子菜单.
您可以在[IntelliJ平台操作系统](/basics/action_system.md)中找到有关如何创建和注册操作的详细信息.


