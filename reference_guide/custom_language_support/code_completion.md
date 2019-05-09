---
title: Code Completion
---

自定义语言插件可以提供两种主要类型的代码完成:引用完成和基于贡献者的完成.


参考完成更容易实现,但仅支持基本完成操作.

基于贡献者的完成提供了更多功能,支持所有三种完成类型(基本,智能和类名),并且可用于实现关键字完成.


###参考完成


要填写完成列表,IDE会调用

[PsiReference.getVariants()](upsource:///platform/core-api/src/com/intellij/psi/PsiReference.java)

要么在插入符号位置的参考,要么在放置在插入符号的虚拟参考上.

此方法需要返回包含字符串的对象数组,

[PsiElement](upsource:///platform/core-api/src/com/intellij/psi/PsiElement.java)

实例或实例

[LookupElement](upsource:///platform/lang-api/src/com/intellij/codeInsight/lookup/LookupElement.java)

类.

如果一个

[PsiElement](upsource:///platform/core-api/src/com/intellij/psi/PsiElement.java)

实例在数组中返回,完成列表显示元素的图标.


实现`getVariants()`的最常用方法是使用相同的函数来向上移动树

[PsiReference.resolve()](upsource:///platform/core-api/src/com/intellij/psi/PsiReference.java),

和不同的实现

[PsiScopeProcessor](upsource:///platform/core-api/src/com/intellij/psi/scope/PsiScopeProcessor.java)

它收集传递给它的`processDeclarations()`方法的所有声明,并将它们作为数组返回以填充完成列表.


###基于贡献者的完成


实施

[CompletionContributor](upsource:///platform/lang-api/src/com/intellij/codeInsight/completion/CompletionContributor.java)

界面使您可以最大程度地控制语言的代码完成操作.

请注意,该类的JavaDoc包含用于实现代码完成的详细FAQ.


使用的核心方案

[CompletionContributor](upsource:///platform/lang-api/src/com/intellij/codeInsight/completion/CompletionContributor.java)

包括调用`extend()`方法并传入* pattern *,指定适用此完成变量的上下文,以及生成要在完成列表中显示的项目的* completion provider *.


**例**:

[CompletionContributor](https://github.com/JetBrains/intellij-plugins/blob/master/osmorc/src/org/osmorc/manifest/completion/OsgiManifestCompletionContributor.java)

用于在MANIFEST.MF文件中完成关键字.


请记住,针对叶PSI元素检查模式.
如果你

想要匹配复合元素,使用`withParent`或`withSuperParent`

方法.


完成列表中显示的项目由实例表示

[LookupElement](upsource:///platform/lang-api/src/com/intellij/codeInsight/lookup/LookupElement.java)

接口.

这些实例通常是通过

[LookupElementBuilder](upsource:///platform/lang-api/src/com/intellij/codeInsight/lookup/LookupElementBuilder.java)

类.

对于每个查找元素,您可以指定以下属性:


*文本,尾部文本和文本类型.
尾部文本显示在主项目文本旁边,不用于前缀匹配,例如,可用于显示方法的参数列表.
例如,类型文本在查找列表中右对齐显示,可用于显示返回类型或包含方法的类.

*图标

*文字属性(粗体,删除线等)

*插入处理程序. 
insert处理程序是在选择项时调用的回调函数,可用于执行文本的其他修改(例如,放入方法调用的括号中)


