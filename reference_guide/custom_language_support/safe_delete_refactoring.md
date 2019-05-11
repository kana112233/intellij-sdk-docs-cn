---
title: Safe Delete Refactoring
---

“安全删除”重构也建立在与“重命名”相同的“查找用法”框架上.

除此之外,为了支持“安全删除”,插件需要实现两件事:


*
   
[RefactoringSupportProvider](upsource:///platform/lang-api/src/com/intellij/lang/refactoring/RefactoringSupportProvider.java)
   
在`com.intellij.lang.refactoringSupport`扩展点中注册的接口,以及`isSafeDeleteAvailable()`方法,该方法检查特定PSI元素的“安全删除”重构是否可用


*
   
[PsiElement.delete()](upsource:///platform/core-api/src/com/intellij/psi/PsiElement.java)<!-- ＃L371-->
   
的方法
   
[PsiElement](upsource:///platform/core-api/src/com/intellij/psi/PsiElement.java)
   
可以使用“安全删除”的子类.
   
删除PSI元素是通过从AST树中删除底层AST节点来实现的(这反过来又会导致从文档中删除与AST节点相对应的文本范围).


**例:**

[删除()](upsource:///plugins/properties/properties-psi-impl/src/com/intellij/lang/properties/psi/impl/PropertyImpl.java)<!-- ＃L363-->

实施一个

[属性语言插件](upsource:///plugins/properties/)


如果需要,可以进一步自定义如何为特定类型的元素执行安全删除(如何搜索引用等).

这是通过实现`SafeDeleteProcessorDelegate`接口完成的.


**例**:

[SafeDeleteProcessorDelegate](upsource:///plugins/properties/src/com/intellij/lang/properties/refactoring/PropertiesFilesSafeDeleteProcessor.java)

实施

[属性语言插件](upsource:///plugins/properties)


