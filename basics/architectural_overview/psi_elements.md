---
title: PSI Elements
---

PSI(程序结构接口)文件表示PSI元素的层次结构(所谓的_PSI trees_).
单个PSI文件(本身是PSI元素)可以包含特定编程语言中的多个PSI树.
反过来,PSI元素可以具有子PSI元素.


PSI元素和各个PSI元素级别的操作用于探索源代码的内部结构,因为它由** IntelliJ平台**解释.
例如,您可以使用PSI元素执行代码分析,例如[代码检查](https://www.jetbrains.com/help/idea/code-inspection.html)或[意图操作](http://www.jetbrains.com/idea/help/intention-actions.html).


[PsiElement](upsource:///platform/core-api/src/com/intellij/psi/PsiElement.java)类是PSI元素的公共基类.


##如何获得PSI元素？


*来自一个动作:`e.getData(LangDataKeys.PSI_ELEMENT)`.
注意:如果编辑器当前处于打开状态且插入符号下的元素是引用,则将返回解析引用的结果.
这可能是您需要的,也可能不是.

*来自偏移的文件:`PsiFile.findElementAt()`.
注意:这将返回指定偏移量处的最低级别元素(“leaf”),该偏移量通常是词法分析器标记.

很可能你应该使用`PsiTreeUtil.getParentOfType()`来找到你真正需要的元素.

*通过迭代PSI文件:使用[`PsiRecursiveElementWalkingVisitor`](upsource:///platform/core-api/src/com/intellij/psi/PsiRecursiveElementWalkingVisitor.java).

*通过解析引用:`PsiReference.resolve()`


##我可以用PSI元素做什么？


参见[PSI Cook Book](/basics/psi_cookbook.md)


