---
title: Navigating the PSI
---

导航PSI有三种主要方式:*自上而下*,*自下而上*,以及使用*引用*.
在第一个场景中,

你有一个PSI文件或另一个更高级别的元素(例如,一个方法),你需要找到所有匹配的元素

指定的条件(例如,所有变量声明).
在第二种情况中,您有一个特定的观点

在PSI树中(例如,插入符号中的元素),需要找出有关其上下文的内容(例如,

它已被声明的元素).
最后, *references* 允许您从元素的使用中导航

(例如,方法调用)声明(被调用的方法)和返回.
参考文献描述于

[单独主题](psi_references.md).


## 自上而下导航


执行自上而下导航的最常用方法是使用 *visitor*.
要使用访问者,您需要创建一个类

(通常是一个匿名内部类)扩展基本访问者类,覆盖处理元素的方法

你感兴趣,并将访问者实例传递给`PsiElement.accept()`.


访问者的基类是特定于语言的.
例如,如果您需要处理Java文件中的元素,

扩展`JavaRecursiveElementVisitor`并覆盖与您所使用的Java元素类型相对应的方法
有兴趣.


以下代码段显示了使用visitor查找所有Java局部变量声明:


```java
file.accept(new JavaRecursiveElementVisitor() {
  @Override
  public void visitLocalVariable(PsiLocalVariable variable) {
    super.visitLocalVariable(variable);
    System.out.println("Found a variable at offset " + variable.getTextRange().getStartOffset());
  }
});
```

在许多情况下,您还可以使用更具体的API进行自上而下导航.
例如,如果您需要获取列表

Java类中的所有方法都可以使用访问者来实现,但更容易实现的方法是调用`PsiClass.getMethods()`.


[PsiTreeUtil类](upsource:///platform/core-api/src/com/intellij/psi/util/PsiTreeUtil.java)包含多个

用于PSI树导航的通用,与语言无关的函数,其中一些(例如,`findChildrenOfType`)

执行自上而下的导航.


## 自下而上的导航


自下而上导航的起点是PSI树中的特定元素(例如,结果)

解析引用)或偏移量.
如果您有偏移量,则可以通过调用找到相应的PSI元素

`PsiFile.findElementAt()`.
此方法返回树的最低级别的元素(例如,标识符),

如果要确定更广泛的上下文,则需要在树中导航.


在大多数情况下,通过调用`PsiTreeUtil.getParentOfType()`来执行自下而上的导航.
这种方法上升了

树,直到找到您指定的类型的元素.
例如,要查找包含方法,请调用

`PsiTreeUtil.getParentOfType(element,PsiMethod.class)`.


在某些情况下,您还可以使用特定的导航方法.
例如,要查找包含方法的类,

你调用`PsiMethod.getContainingClass()`.


以下代码段显示了如何一起使用这些调用:


```java
PsiFile psiFile = anActionEvent.getData(CommonDataKeys.PSI_FILE);
PsiElement element = psiFile.findElementAt(offset);
PsiMethod containingMethod = PsiTreeUtil.getParentOfType(element, PsiMethod.class);
PsiClass containingClass = containingMethod.getContainingClass();
```

要了解导航在实践中的工作原理,请参阅

[代码示例](https://github.com/JetBrains/intellij-sdk-docs/blob/master/code_samples/psi_demo/src/com/intellij/tutorials/psi/PsiNavigationDemoAction.java).


