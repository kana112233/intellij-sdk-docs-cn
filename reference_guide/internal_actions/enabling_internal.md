---
title: Enabling Internal Mode
---

有一些有用的工具,例如“内部操作”菜单,只有在IntelliJ IDEA中启用了内部模式时才可见.


## Setting Internal Mode in the IDE Properties File
There are multiple ways to enable internal mode, but the simplest is within IntelliJ IDEA:
* Start IntelliJ IDEA.
* From the main menu, select **Help | Edit Custom Properties**.
This selection opens IntelliJ IDEA's `idea.properties` file.
If it does not exist, IntelliJ IDEA will prompt to create one.
* Add the line shown below to the `idea.properties` file:
```
idea.is.internal=true
```
* Save the `idea.properties` file and restart IntelliJ IDEA.

The Internal Actions menu is available in **Tools \| Internal Actions**.



