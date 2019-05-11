---
title: Rename Refactoring
---

重命名重构的操作与Find Usages的操作非常相似.

它使用相同的规则来定位要重命名的元素,以及用于定位文件的相同索引,这些文件可能引用了要重命名的元素.


当执行重命名重构时,该方法

[PsiNamedElement.setName()](upsource:///platform/core-api/src/com/intellij/psi/PsiNamedElement.java)

调用重命名的元素,和

[PsiReference.handleElementRename()](upsource:///platform/core-api/src/com/intellij/psi/PsiReference.java)

调用所有对重命名元素的引用.

这两种方法基本上执行相同的操作:将PSI元素的基础AST节点替换为包含用户输入的新文本的节点.

从头开始创建完全正确的AST节点非常困难.

因此,令人惊讶的是,获取替换节点的最简单方法是使用自定义语言创建一个虚拟文件,以便它在其解析树中包含必要的节点,构建解析树并从中提取必要的节点.


**例:**

[的setName()](upsource:///plugins/properties/properties-psi-impl/src/com/intellij/lang/properties/psi/impl/PropertyImpl.java)<!-- ＃L58-->

实施一个

[属性语言插件](upsource:///plugins/properties)


与重命名重构相关的另一个接口是

[NamesValidator](upsource:///platform/lang-api/src/com/intellij/lang/refactoring/NamesValidator.java).

此接口允许插件根据自定义语言规则检查用户在“重命名”对话框中输入的名称是否是有效标识符(而不是关键字).

如果插件未提供此接口的实现,则使用用于验证标识符的Java规则.

实施

[NamesValidator](upsource:///platform/lang-api/src/com/intellij/lang/refactoring/NamesValidator.java)

在`com.intellij.lang.namesValidator`扩展点中注册.


**例**:

[NamesValidator](upsource:///plugins/properties/src/com/intellij/lang/properties/PropertiesNamesValidator.java)

对于

[属性语言插件](upsource:///plugins/properties)


可以在多个级别上进一步自定义重命名重构处理.

提供自定义的实现

[RenameHandler](upsource:///platform/lang-api/src/com/intellij/refactoring/rename/RenameHandler.java)

界面允许您完全替换重命名重构的UI和工作流,并且还支持重命名非重构的内容

[PsiElement](upsource:///platform/core-api/src/com/intellij/psi/PsiElement.java)

一点都不


**例**:

[RenameHandler](upsource:///plugins/properties/src/com/intellij/lang/properties/refactoring/rename/ResourceBundleFromEditorRenameHandler.java)

用于重命名资源包

[属性语言插件](upsource:///plugins/properties)


如果你对标准UI很好,但需要扩展默认的重命名逻辑,你可以提供一个实现

[RenamePsiElementProcessor](upsource:///platform/lang-impl/src/com/intellij/refactoring/rename/RenamePsiElementProcessor.java)

接口.

这允许您:


*重命名与调用操作的元素不同的元素(例如,超级方法)


*一次重命名多个元素(如果他们的名字根据您的语言逻辑链接)


*检查名称冲突(现有名称等)


*自定义搜索代码引用或文本引用的方式


*等


**例**:

[RenamePsiElementProcessor](upsource:///plugins/properties/src/com/intellij/lang/properties/refactoring/rename/RenamePropertyProcessor.java)

用于重命名属性

[属性插件语言](upsource:///plugins/properties)


