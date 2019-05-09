---
title: Working with Icons and Images
---

IntelliJ平台插件广泛使用图标和图像.
插件需要图标主要用于操作,自定义组件渲染器,工具窗口等.


> **注意**插件图标代表插件本身,与插件中使用的图标和图像有不同的要求.

有关更多信息,请参阅[插件图标](/basics/plugin_structure/plugin_icon_file.md)页面.


> **提示**插件应尽可能重用现有平台图标,请参阅[AllIcons](upsource:///platform/util/src/com/intellij/icons/AllIcons.java).
详细的[设计指南](https://jetbrains.github.io/ui/principles/icons/)可用于创建自定义图标.
  

##如何组织和如何使用图标？


处理图标和其他图像资源的最佳方法是将它们放到专用的源根,比如*“icons”*或*“resources”*.


![图标](IMG/icons1.png)


[`com.intellij.openapi.util.IconLoader`]的`getIcon()`方法(upsource:///platform/util/src/com/intellij/openapi/util/IconLoader.java)可用于访问
图标.
然后在名为`icons`的顶级包中定义一个带有图标常量的类或接口:


```java
package icons;

public interface DemoPluginIcons {
  Icon STRUCTURE_TOOL_WINDOW = IconLoader.getIcon("/icons/toolWindowStructure.png");
  Icon MY_LANG_FILE_TYPE = IconLoader.getIcon("/icons/myLangFileType.png");
  Icon DEMO_ACTION = IconLoader.getIcon("/icons/demoAction.png");
}
```

在`plugin.xml`中也使用这些常量.
请注意,包名称“icons”将自动添加前缀,不应手动添加.


```xml
<action id="DemoPlugin.DemoAction"
        class="com.jetbrains.demoplugin.actions.DemoAction"
        text="Demo Action"
        description="This is just a demo"
        icon="DemoPluginIcons.DEMO_ACTION"/>
```

###图像格式


IntelliJ平台支持Retina显示,并具有名为Darcula的黑暗主题.
因此,每个图标都应该有Retina设备和Darcula主题的专用变体.
在某些情况下,如果原始图标在Darcula下看起来很好,您可以跳过黑暗变体.


所需的图标大小取决于下表中列出的用法:


| Usage | Icon Size (pixels) |
|-------|--------------------|
| Node, Action, Filetype | 16x16 |
| Tool window            | 13x13 |
| Editor gutter          | 12x12 |

#### SVG格式

> **注意**自2018.2起支持SVG图标.


由于SVG图标可以任意缩放,因此它们可以在HiDPI环境中提供更好的结果,或者与更大的屏幕字体结合使用时(例如,在演示模式下).


应提供以1x比例表示渲染图像的大小(在用户空间中)的基本大小.
通过省略大小单位的`width`和`height`属性设置大小.
如果未指定,则默认为16x16像素.


A minimal SVG icon file:
```xml
<svg xmlns="http://www.w3.org/2000/svg" width="16" height="16">
 <rect width="100%" height="100%" fill="green"/>
</svg>
```

用于PNG图标的命名符号(见下文)仍然相关.
但是,SVG图标的`@ 2x`版本仍应提供相同的基本大小.
可以通过双精度更详细地表达这种图标的图标图形.
如果图标图形足够简单,以便在每个比例下完美呈现,那么可以省略`@ 2x`版本.


#### PNG格式

> **注意**如果插件的目标是2018.2+,请考虑使用SVG图标.


所有图标文件必须放在此命名模式后的同一目录中(将`.png`替换为`.svg`用于SVG图标):


* ** iconName.png ** W x H像素(将在具有默认主题的非Retina设备上使用)

* **iconName@2x.png** 2 \ * W x 2 \ * H像素(将在具有默认主题的Retina设备上使用)

* ** iconName_dark.png ** W x H像素(将在具有Darcula主题的非Retina设备上使用)

* **iconName@2x_dark.png** 2 \ * W x 2 \ * H像素(将用于具有Darcula主题的Retina设备)


`IconLoader`类将根据当前环境加载与最佳匹配的图标.


以下是* toolWindowStructure.png *图标表示的示例:


| Theme/Resolution | File name                         | Image |
|------------------|-----------------------------------|-------|
| Default          | `toolWindowStructure.png`         | ![Tool Window Structure](img/toolWindowStructure.png) |
| Darcula          | `toolWindowStructure_dark.png`    | ![Tool Window Structure, dark](img/toolWindowStructure_dark.png) |
| Default + Retina | `toolWindowStructure@2x.png`      | ![Tool Window Structure, retina](img/toolWindowStructure@2x.png) |
| Darcula + Retina | `toolWindowStructure@2x_dark.png` | ![Tool Window Structure, retina, dark](img/toolWindowStructure@2x_dark.png) |

