---
title: Internal Actions - UI Inspector
---

_UI Inspector_是一个询问IntelliJ IDEA UI元素的工具,以获取每个元素的内部描述.

在启用_UI Inspector_时,可以通过单击元素以交互方式测试UI元素.


If the menu item **Tools \| Internal Actions \| UI \| UI Inspector** is not available in IntelliJ IDEA, then the first step is to [enable internal mode](enabling_internal.md)

## Enabling the UI Inspector
Before using the _UI Inspector_, it must be enabled by selecting the menu item **Tools \| Internal Actions \| UI \| UI Inspector**.
The enabled state of the _UI Inspector_ is modal; it remains enabled until it is disabled by selecting the _UI Inspector_ menu item again. 

启用后,_UI Inspector_菜单项会显示一个复选标记.


##使用UI检查器

启用时,将光标居中于UI元素上,并在_clicking_鼠标时按_control-alt_显示UI元素的内部描述.


例如,要获取有关工具栏上的构建(锤子)图标的信息,请将光标放在图标上,然后在单击鼠标时按_control-alt_.

_UI Inspector_显示图标的内部路径为“Allcons.Actions.Compile”:


![内部图标信息](img/internal_uii_icon_info.png)


