---
title: Go to Class and Go to Symbol
---

A custom language plugin can provide its own items to be included in the lists shown when the user chooses the `Go to | Class...` or `Go to | Symbol...` action.
In order to do so, the plugin must provide implementations for the
[ChooseByNameContributor](upsource:///platform/lang-api/src/com/intellij/navigation/ChooseByNameContributor.java)
interface (separate implementations need to be provided for `Go to Class` and `Go to Symbol`), and register them in the `com.intellij.gotoClassContributor` and `com.intellij.gotoSymbolContributor` extension points.

每个贡献者都需要能够返回一个完整的名称列表,以便在指定项目的列表中显示,然后IDE将根据用户在对话框中键入的文本对其进行过滤.

对于该列表中的每个名称,贡献者需要提供列表

[NavigationItem](upsource:///platform/core-api/src/com/intellij/navigation/NavigationItem.java)

实例(通常

[PsiElement](upsource:///platform/core-api/src/com/intellij/psi/PsiElement.java)

),指定从列表中选择特定名称时要跳转到的目的地.


