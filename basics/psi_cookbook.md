---
title: PSI Cookbook
---

此页面提供了使用PSI(程序结构接口)的最常见操作的配方列表.
与[开发自定义语言插件](/reference_guide/custom_language_support.md)不同,它讨论了使用现有语言(例如Java)的PSI.


##如果我知道它的名字但是不知道路径,我如何找到文件？


`FilenameIndex.getFilesByName()`


##如何找到特定PSI元素的使用位置？


`ReferencesSearch.search()`


##如何重命名PSI元素？


`RefactoringFactory.createRename()`


##如何重建虚拟文件的PSI？


`FileContentUtil.reparseFiles()`


## Java Specific


###如何找到类的所有继承者？


`ClassInheritorsSearch.search()`


###如何通过限定名称查找课程？


`JavaPsiFacade.findClass()`


###如何通过短名称找到课程？


`PsiShortNamesCache.getInstance().getClassesByName()`


###如何找到Java类的超类？


`PsiClass.getSuperClass()`


###如何获取对Java类的包含的引用？


```java
PsiJavaFile javaFile = (PsiJavaFile) psiClass.getContaningFile();
PsiPackage pkg = JavaPsiFacade.getInstance(project).findPackage(javaFile.getPackageName());
```

###如何找到覆盖特定方法的方法？


`OverridingMethodsSearch.search()`


