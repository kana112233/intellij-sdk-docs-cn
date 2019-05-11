---
title: Find Usages
---

“查找用法”操作是一个多步骤的过程,该过程的每个步骤都需要自定义语言插件的参与.

语言插件通过注册实现来参与Find Usages过程

[FindUsagesProvider](upsource:///platform/indexing-api/src/com/intellij/lang/findUsages/FindUsagesProvider.java)

在`com.intellij.lang.findUsagesProvider`扩展点,并通过PSI实现使用

[PsiNamedElement](upsource:///platform/core-api/src/com/intellij/psi/PsiNamedElement.java)

和

[PsiReference](upsource:///platform/core-api/src/com/intellij/psi/PsiReference.java)

接口.


**例**:

实施

[FindUsagesProvider](upsource:///plugins/properties/properties-psi-impl/src/com/intellij/lang/properties/findUsages/PropertiesFindUsagesProvider.java)

在

[属性语言插件](upsource:///plugins/properties/)


“查找用法”操作的步骤如下:


*在调用“查找用法”操作之前,IDE会构建自定义语言中每个文件中存在的单词索引.
   
使用
   
[WordsScanner](upsource:///platform/indexing-api/src/com/intellij/lang/cacheBuilder/WordsScanner.java)
   
实现返回
   
[FindUsagesProvider.getWordsScanner()](upsource:///platform/indexing-api/src/com/intellij/lang/findUsages/FindUsagesProvider.java),
   
加载每个文件的内容并将其传递给单词scanner,以及单词consumer.
   
单词scanner scan将文本分成单词,定义每个单词的上下文(代码,注释或文字)并将单词传递给消费者.
   
实现单词扫描程序的最简单方法是使用
   
[DefaultWordsScanner](upsource:///platform/indexing-api/src/com/intellij/lang/cacheBuilder/DefaultWordsScanner.java)
   
实现,传递lexer令牌类型的集合,这些类型被视为标识符,文字和注释.
   
默认单词scanner scan将使用词法分析器将文本分解为标记,并将处理注释和文字标记的文本分成单个单词.


*当用户调用Find Usages操作时,IDE将找到要搜索的引用的PSI元素.
   
游标处的PSI元素(光标位置处令牌的直接树父)必须是a
   
[PsiNamedElement](upsource:///platform/core-api/src/com/intellij/psi/PsiNamedElement.java)
   
或者a
   
[PsiReference](upsource:///platform/core-api/src/com/intellij/psi/PsiReference.java)
   
哪个解决了
   
[PsiNamedElement](upsource:///platform/core-api/src/com/intellij/psi/PsiNamedElement.java).
   
单词缓存将用于搜索从中返回的文本
   
[PsiNamedElement.getName()](upsource:///platform/core-api/src/com/intellij/psi/PsiNamedElement.java)
   
方法.
   
另外,如果是文本范围的话
   
[PsiNamedElement](upsource:///platform/core-api/src/com/intellij/psi/PsiNamedElement.java)
   
除了从`getName()`返回的标识符之外还包括一些其他文本(例如,如果是
   
[PsiNamedElement](upsource:///platform/core-api/src/com/intellij/psi/PsiNamedElement.java)
   
代表一个JavaScript函数,它的文本范围除了包含函数名称之外还包含“`function`”关键字,必须覆盖`getTextOffset()`方法.
   
[PsiNamedElement](upsource:///platform/core-api/src/com/intellij/psi/PsiNamedElement.java),
   
并且必须在元素的文本范围内返回名称标识符的起始偏移量.


*找到元素后,IDE会调用
   
[FindUsagesProvider.canFindUsagesFor()](upsource:///platform/indexing-api/src/com/intellij/lang/findUsages/FindUsagesProvider.java)
   
如果Find Usages操作适用于特定元素,请询问插件.


*向用户显示“查找用户”对话框时,
   
[FindUsagesProvider.getType()](upsource:///platform/indexing-api/src/com/intellij/lang/findUsages/FindUsagesProvider.java)
   
和
   
[FindUsagesProvider.getDescriptiveName()](upsource:///platform/indexing-api/src/com/intellij/lang/findUsages/FindUsagesProvider.java)
   
被调用以确定如何将元素呈现给用户.


*对于包含搜索词的每个文件,IDE构建PSI树并递归地下降该树.
   
每个元素的文本被分成单词然后被扫描.
   
如果元素被索引为标识符,则每个单词都被检查为a
   
[PsiReference](upsource:///platform/core-api/src/com/intellij/psi/PsiReference.java)
   
解析搜索其用法的元素.
   
如果元素被索引为注释或文字,并且启用了注释或文字中的搜索,则会检查该单词是否等于搜索元素的名称.


*收集使用后,结果将显示在用法窗格中.

为每个找到的元素显示的文本取自

[FindUsagesProvider.getNodeText()](upsource:///platform/indexing-api/src/com/intellij/lang/findUsages/FindUsagesProvider.java)

方法.


要在Find Usages工具窗口的标题中正确显示找到的元素的标题,您需要提供

[ElementDescriptionProvider](upsource:///platform/core-api/src/com/intellij/psi/ElementDescriptionProvider.java)

接口.

该

[ElementDescriptionLocation](upsource:///platform/core-api/src/com/intellij/psi/ElementDescriptionLocation.java)

在这种情况下传递给提供者将是一个实例

[UsageViewLongNameLocation](upsource:///platform/lang-impl/src/com/intellij/usageView/UsageViewLongNameLocation.java).


**例:**

[ElementDescriptionProvider](upsource:///plugins/properties/src/com/intellij/lang/properties/PropertiesDescriptionProvider.java)

对于

[属性语言插件](upsource:///plugins/properties/)


> **提示**在函数参数和局部变量等情况下,请考虑覆盖

[PsiElement# getUseScope](upsource:///platform/core-api/src/com/intellij/psi/PsiElement.java)返回较窄的范围.

例如,您可能只返回最近函数定义的范围.
这种优化可以大大减少

重命名函数参数时需要解析的文件数 - 以及需要解析的引用

或局部变量.


