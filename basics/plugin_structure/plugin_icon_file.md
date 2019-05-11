---
title: Plugin Icon
---

从版本2019.1开始,IntelliJ平台支持使用图标表示插件.

_Plugin Icon_旨在成为插件功能,技术或的独特表示

公司.


插件图标显示在设置/首选项[插件管理器](https://www.jetbrains.com/help/idea/managing-plugins.html)UI中

在JetBrains的产品中.
插件图标也出现在[Plugins Repository](https://plugins.jetbrains.com/)中,

以及[MarketPlace](https://plugins.jetbrains.com/marketplace).


**注意:**插件中使用的图标和图像有不同的要求.

请参阅[使用图标和图像](/reference_guide/work_with_icons_and_images.md)

欲获得更多信息.


### 插件图标文件格式

所有插件图标图像必须以SVG格式提供.

插件图标将以40px x 40px和80px x 80px大小显示.
但是,只有一个

应提供size icon_,因为它会自动生成

规模.


| Icon Size  | Example SVG Icon                            |
| ------------- | -------------                            |
| 40x40         | ![Kotlin Plugin Icon](img/kotlin40.svg)  |

### 插件图标文件命名约定

插件图标文件根据以下约定命名:
* `pluginIcon.svg`用于默认(轻量)JetBrains IDE主题
* `pluginIcon_dark.svg`用于Darcula JetBrains IDE主题


### 将插件图标添加到插件项目中

插件图标文件必须位于插件分发文件的“META-INF”文件夹中,
即您上传到插件存储库并安装到的`* .jar`或`* .zip`文件
JetBrains IDE.


要在分发文件中包含插件图标,请放置插件图标文件

进入插件项目的`resources/META-INF`文件夹.
注意这个要求

无论使用DevKit还是Gradle开发插件,都是一样的.
例如:
 

![META-INF文件夹中的插件图标](img/resource_directory_structure.png)


