---
title: References and Resolve
---

实现自定义语言PSI最重要和最棘手的部分之一是解析引用.

解析引用使用户能够从PSI元素用法(访问变量,调用方法等)导航到该元素的声明(变量的定义,方法声明等).

需要此功能才能支持** Ctrl-B **和** Ctrl-Click **调用的“Go to Declaration”操作,这是实现`Find Usages`操作的先决条件,`Rename 
`重构和代码完成.


所有作为参考的PSI元素(适用于“Go to Declaration”操作)都需要实现

[PsiElement.getReference()](upsource:///platform/core-api/src/com/intellij/psi/PsiElement.java)

方法并返回一个

[PsiReference](upsource:///platform/core-api/src/com/intellij/psi/PsiReference.java)

从该方法实现.

该

[PsiReference](upsource:///platform/core-api/src/com/intellij/psi/PsiReference.java)

接口可以由同一个类实现

[PsiElement](upsource:///platform/core-api/src/com/intellij/psi/PsiElement.java),

或者由另一个班级.
元素也可以包含多个引用(例如,字符串文字可以包含多个子字符串,这些子字符串是有效的完全限定类名),在这种情况下它可以实现

[PsiElement.getReferences()](upsource:///platform/core-api/src/com/intellij/psi/PsiElement.java)

并将引用作为数组返回.


主要方法是

[PsiReference](upsource:///platform/core-api/src/com/intellij/psi/PsiReference.java)

interface是`resolve()`,它返回引用指向的元素,如果无法解析对有效元素的引用,则返回“null”(例如,它应该指向未定义的类).
已解析的元素应实现[PsiNamedElement](upsource:///platform/core-api/src/com/intellij/psi/PsiNamedElement.java)接口.


> **注意**虽然引用元素和引用元素都有一个名称,但只有**引入**名称的元素(例如定义`int x = 42`)需要实现[PsiNamedElement]( 
upsource:///platform/core-api/src/com/intellij/psi/PsiNamedElement.java)界面.
使用时的引用元素(例如表达式“x + 1”中的`x`)不必实现[PsiNamedElement](upsource:///platform/core-api/src/com/intellij) 
/psi/PsiNamedElement.java).


> **提示**为了启用更高级的IntelliJ功能,更喜欢在[PsiNamedElement]上实现[PsiNameIdentifierOwner](upsource:///platform/core-api/src/com/intellij/psi/PsiNameIdentifierOwner.java)(upsource 
:///platform/core-api/src/com/intellij/psi/PsiNamedElement.java)尽可能.


`resolve()`方法的对应部分是`isReferenceTo()`,它检查引用是否解析为指定的元素.
后一种方法可以通过调用`resolve()`并将结果与​​传递的PSI元素进行比较来实现,但是可以进行额外的优化(例如,仅当元素文本等于引用的文本时才执行树遍历) 
.


**例**:

[参考文献](upsource:///plugins/properties/src/com/intellij/lang/properties/ResourceBundleReference.java)

到了一个ResourceBundle

[属性语言插件](upsource:///plugins/properties)


有一组接口可以作为实现解析支持的基础,即

[PsiScopeProcessor](upsource:///platform/core-api/src/com/intellij/psi/scope/PsiScopeProcessor.java)界面和

[PsiElement.processDeclarations()](upsource:///platform/core-api/src/com/intellij/psi/PsiElement.java)

方法.

这些接口具有许多额外的复杂性,这些复杂性对于大多数自定义语言来说不是必需的(例如支持替换Java泛型类型),但如果自定义语言可以引用Java代码,则它们是必需的.

如果不需要Java互操作性,插件可以放弃标准接口并提供自己的,不同的解析实现.


基于标准辅助类的resolve的实现包含以下组件:


*一个实现的类
   
[PsiScopeProcessor](upsource:///platform/core-api/src/com/intellij/psi/scope/PsiScopeProcessor.java)
   
接口,它收集引用的可能声明,并在成功完成后停止解析过程.
   
需要实现的主要方法是`execute()`,它被调用来处理在解析期间遇到的每个声明,如果需要继续解析则返回“true”,如果找到声明则返回“false”.
   
方法`getHint()`和`handleEvent()`用于内部优化,可以留空
   
[PsiScopeProcessor](upsource:///platform/core-api/src/com/intellij/psi/scope/PsiScopeProcessor.java)
   
自定义语言的实现.


*一个函数,它从参考位置向上走PSI树,直到成功完成解析或直到达到解析范围的末尾.
   
如果引用的目标位于不同的文件中,则可以定位文件,例如,使用
   
[FilenameIndex.getFilesByName()](upsource:///platform/indexing-impl/src/com/intellij/psi/search/FilenameIndex.java)
   
(如果文件名已知)或者通过迭代项目中的所有自定义语言文件(`iterateContent()`
   
[FileIndex](upsource:///platform/indexing-impl/src/com/intellij/psi/search/FilenameIndex.java)
   
界面获得
   
[ProjectRootManager.getFileIndex()](upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/ProjectRootManager.java)
   
).


*各个PSI元素,在PSI树步行期间调用`processDeclarations()`方法.
   
如果PSI元素是一个声明,它会将自身传递给.的`execute()`方法
   
[PsiScopeProcessor](upsource:///platform/core-api/src/com/intellij/psi/scope/PsiScopeProcessor.java)
   
传递给它.
   
此外,根据语言范围规则,如果需要,PSI元素可以通过
   
[PsiScopeProcessor](upsource:///platform/core-api/src/com/intellij/psi/scope/PsiScopeProcessor.java)
   
它的子元素.


延伸的

[PsiReference](upsource:///platform/core-api/src/com/intellij/psi/PsiReference.java)

接口,允许引用解析为多个目标,是

[PsiPolyVariantReference](upsource:///platform/core-api/src/com/intellij/psi/PsiPolyVariantReference.java)

接口.

引用解析的目标从`multiResolve()`方法返回.

针对此类引用的“转至声明”操作允许用户选择导航目标.

`multiResolve()`的实现也可以基于

[PsiScopeProcessor](upsource:///platform/core-api/src/com/intellij/psi/scope/PsiScopeProcessor.java),

并且可以收集所有有效目标以供参考,而不是在找到第一个有效目标时停止.


“快速定义查找”操作基于与“转到声明”相同的机制,因此它可以自动用于语言插件可以解析的所有引用.


