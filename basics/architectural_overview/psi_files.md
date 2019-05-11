---
title: PSI Files
---

PSI(程序结构接口)文件是表示文件内容的结构的根,作为特定编程语言中元素的层次结构.


[PsiFile](upsource:///platform/core-api/src/com/intellij/psi/PsiFile.java)类是所有PSI文件的公共基类,而特定语言的文件通常由其表示
子类.
例如,[PsiJavaFile](upsource:///java/java-psi-api/src/com/intellij/psi/PsiJavaFile.java)类表示Java文件,[XmlFile](upsource:///xml/xml-psi-api/src/com/intellij/psi/xml/XmlFile.java)类表示XML文件.


与具有应用范围的`VirtualFile`和`Document`不同(即使多个项目是打开的,每个文件由相同的`VirtualFile`实例表示),PSI也有项目范围(同一个文件由多个`PsiFile`实例表示)
如果文件属于同时打开的多个项目).


## 如何获取PSI文件？


* 来自一个动作:`e.getData(LangDataKeys.PSI_FILE)`.

* 来自VirtualFile:`PsiManager.getInstance(project).findFile()`

* 来自Document:`PsiDocumentManager.getInstance(project).getPsiFile()`

* 来自文件中的元素:`psiElement.getContainingFile()`

* 要在项目的任何位置查找具有特定名称的文件,请使用`FilenameIndex.getFilesByName(项目,名称,范围)`


## 如何处理PSI文件？


最有趣的修改操作是在单个PSI元素的级别上执行的,而不是作为整体的文件.


要迭代文件中的元素,请使用`psiFile.accept(new PsiRecursiveElementWalkingVisitor()...);`


## PSI文件来自哪里？


由于PSI依赖于语言,因此通过使用“LanguageParserDefinitions.INSTANCE”,通过[语言](upsource:///platform/core-api/src/com/intellij/lang/Language.java)对象创建PSI文件. 
forLanguage(language).createFile(fileViewProvider)`方法.


与文档类似,当为特定文件访问PSI时,将按需创建PSI文件.


## PSI文件持续多长时间？


与文档一样,PSI文件从相应的`VirtualFile`实例中被弱引用,如果没有任何人引用,则可以进行垃圾收集.


## 如何创建PSI文件？


[`PsiFileFactory`](upsource:///platform/core-api/src/com/intellij/psi/PsiFileFactory.java)`.getInstance(project).createFileFromText()`方法创建一个内存中的PSI文件
指定的内容.


要将PSI文件保存到磁盘,请使用[`PsiDirectory`](upsource:///platform/core-api/src/com/intellij/psi/PsiDirectory.java)`.add()`方法.


## 如何在PSI文件发生变化时收到通知？


`PsiManager.getInstance(project).addPsiTreeChangeListener()`允许您接收有关项目PSI树的所有更改的通知.


## 如何扩展PSI？


可以通过自定义语言插件扩展PSI以支持其他语言.
有关开发自定义语言插件的更多详细信息,请参阅[自定义语言支持](/reference_guide/custom_language_support.md)参考指南.


## 使用PSI有哪些规则？


对PSI文件内容所做的任何更改都会反映在文档中,因此处理文档的所有规则(读/写操作,命令,只读状态处理)都有效.


