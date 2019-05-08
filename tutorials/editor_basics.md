---
title: Basics of working with the Editor
---

[源代码](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/editor_basics)


----------

本教程将引导您完成一系列步骤，说明如何使用IntelliJ Platform Editor，如何访问和修改它包含的文本，

以及如何处理发送给编辑器的事件。


* [1。
使用文本](editor_basics/working_with_text.md)

* [2。
编辑器坐标系。
位置和偏移](editor_basics/coordinates_system.md)

* [3。
处理编辑器事件](editor_basics/editor_events.md)


**注意:**所描述的API部分仅允许使用文本进行操作。

如果您需要访问PSI，请参阅

[PSI Cookbook](/basics/psi_cookbook.md)

部分。


**也可以看看:**

[editor-ui-api package](upsource:///platform/editor-ui-api)，

[Editor.java](upsource:///platform/editor-ui-api/src/com/intellij/openapi/editor/Editor.java)，

[EditorImpl.java](upsource:///platform/platform-impl/src/com/intellij/openapi/editor/impl/EditorImpl.java)。

[CommonDataKeys.java](upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem/CommonDataKeys.java)，

[DataKey.java](upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem/DataKey.java)，

[AnActionEvent](upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnActionEvent.java)，

[DataContext的](upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem/DataContext.java)


**相关话题:**

[Action System](/tutorials/action_system.md)




