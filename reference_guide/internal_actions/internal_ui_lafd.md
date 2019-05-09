---
title: Internal Actions - LaF Defaults
---

_LaF Defaults_窗口为UI控件提供键值对查找.

它还允许UI Control颜色更改的交互式原型设计.


If the menu item **Tools \| Internal Actions** is not available in IntelliJ IDEA, then the first step is to [enable internal mode](enabling_internal.md)

## Opening LaF Defaults
The _LaF Defaults_ window is opened by selecting the menu item **Tools \| Internal Actions \| UI \| LaF Defaults**.

_LaF Defaults_窗口有两列表示UI控件的键值对:

* _Name_列包含运行时可用的每个IntelliJ Platform UI元素的UI Control`键.

* _Value_列包含每个IntelliJ Platform UI元素的UI Control颜色“value”.


##使用LaF面板


###查找UI控件

通过输入UI元素类型以交互方式使用_LaF Defaults_窗口 - 例如
`Panel`  - 位于顶部的_Filter_文本框中.

_LaF Defaults_显示与过滤器匹配的UI控件名称列表.

单击其中一个名称会缩小信息以仅显示该UI元素的键值对:


![LaF Lookup](img/internal_lafd_win.png)


###原型设计UI控件的颜色

通过单击感兴趣的_Name_(`key`)旁边的_Value_列,可以(实时)更改UI控件的颜色.

将显示_Choose Color_窗口.

颜色更改可以指定为RGB,十六进制或使用颜色选择器.

按_Choose_按钮可立即更改UI Control颜色.


可以使用_Choose Color_窗口重置UI控件颜色,或重置[UI主题](https://www.jetbrains.com/help/idea/settings-appearance.html).




