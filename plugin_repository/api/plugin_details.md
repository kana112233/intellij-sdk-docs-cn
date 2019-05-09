---
title: Plugin Details API
---

This API endpoint provides individual plugin details by specified *pluginId* or *pluginXmlId*:
 
```
https://plugins.jetbrains.com/plugins/list?pluginId=<pluginXmlId>
```
or
```
https://plugins.jetbrains.com/plugins/list?pluginId=<pluginId>
```

哪里


* ** pluginXmlId **被指定为插件的单个更新页面右侧和plugin.xml中的* Plugin XML ID *参数;


* ** pluginId **是插件的数字ID,可以从插件存储库URL中检索.
例如
[Scala](https://plugins.jetbrains.com/plugin/1347-scala)插件ID为* 1347 *.


例如
[Scala插件](https://plugins.jetbrains.com/plugin/1347-scala):


[https://plugins.jetbrains.com/plugins/list?pluginId=org.intellij.scala](https://plugins.jetbrains.com/plugins/list?pluginId=org.intellij.scala)

要么

[https://plugins.jetbrains.com/plugins/list?pluginId=1347](https://plugins.jetbrains.com/plugins/list?pluginId=1347)


