---
title: Download Plugin Update API
---

有两种方法可以编程方式下载插件版本:


## 下载与某个产品兼容的最新插件更新


```
https://plugins.jetbrains.com/pluginManager?action=download&id=<pluginXmlId>&build=<productCode>-<buildNumber>
```

哪里


* ** pluginXmlId **被指定为插件的单个更新页面右侧和plugin.xml中的* Plugin XML ID *参数;


* ** productCode **是一个双字符产品代码.
可以是空白的.


* ** buildNumber **是IDE的内部版本号(在产品的“关于对话框”和发行说明中指定).


例如
[Scala插件](https://plugins.jetbrains.com/plugin/1347-scala)(`pluginXmlId = org.intellij.scala`)＆IntelliJ IDEA 2017.1(`productCode = IU`,`buildNumber = 171.3780.107 
`):


`的https://plugins.jetbrains.com/pluginManager行动=下载＆ID = org.intellij.scala及建立= IU-171.3780.107`


## 下载指定版本的插件


```
https://plugins.jetbrains.com/plugin/download?pluginId=<pluginXmlId>&version=<version>
```

哪里


* ** pluginXmlId **被指定为插件的单个更新页面右侧和plugin.xml中的* Plugin XML ID *参数;


* ** version **是插件更新的一个版本.


例如
[Scala插件](https://plugins.jetbrains.com/plugin/1347-scala)(`pluginXmlId = org.intellij.scala`)和版本`2017.1.15`:


`的https://plugins.jetbrains.com/plugin/download pluginId = org.intellij.scala＆版本= 2017.1.15`


