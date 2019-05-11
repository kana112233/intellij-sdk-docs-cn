---
title: Surround With
---

In order to support the `Code | Surround With...` action, the plugin needs to register one or more implementations of the
[SurroundDescriptor](upsource:///platform/lang-api/src/com/intellij/lang/surroundWith/SurroundDescriptor.java)
interface in the `com.intellij.lang.surroundDescriptor` extension point.
Each of the surround descriptors defines a possible type of code fragment which can be surrounded - for example, one surround descriptor can handle surrounding expressions, and another can handle statements.
Each surround descriptor, in turn, contains an array of
[Surrounder](upsource:///platform/lang-api/src/com/intellij/lang/surroundWith/Surrounder.java)
objects, defining specific templates which can be used for surrounding the selected code fragment (for example, `Surround With if`, `Surround With for` and so on).

当调用`Surround With ...`动作时,IDE将查询该语言的所有环绕描述符,直到它找到一个从其`getElementsToSurround()`方法返回非空数组的描述符.

然后它调用

[Surrounder.isApplicable()](upsource:///platform/lang-api/src/com/intellij/lang/surroundWith/Surrounder.java)<!-- # L46-->

该描述符中每个surrounder的方法,用于检查特定模板是否适用于当前上下文.

一旦用户从弹出菜单中选择了特定的surrounder,就可以了

[Surrounder.surroundElements()](upsource:///platform/lang-api/src/com/intellij/lang/surroundWith/Surrounder.java)<!-- # L57-->

方法用于执行环绕动作.


