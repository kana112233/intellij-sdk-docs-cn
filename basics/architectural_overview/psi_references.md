---
title: PSI References
---

PSI树中的* reference *是一个对象,表示代码中某个元素的* usage *的链接

到相应的*声明*. 
*解析*引用意味着找到特定用法的声明

指.


最常见的引用类型由语言语义定义.
例如,考虑一个简单的Java方法:


```java
public void hello(String message) {
    System.out.println(message);
}
```

这个简单的代码片段包含五个引用.
由标识符`String`,`System`,`out`和.创建的引用

`println`可以解析为JDK中的相应声明:`String`和`System`类,`out`字段和

`println`方法.
由`println(message)`中第二次出现的`message`标识符创建的引用可以解析为

`message`参数,由方法头中的`String message`声明.


请注意,`String message`不是引用,无法解析.
相反,它是_declaration_.
它不是

指其他地方定义的任何名称;
相反,它自己定义一个名称.


引用是实现[`PsiReference`](upsource:///platform/core-api/src/com/intellij/psi/PsiReference.java)接口的类的实例.

请注意,引用与PSI元素不同.
您可以通过调用获取PSI元素创建的引用

`PsiElement.getReferences()`,可以通过调用`PsiReference.getElement()`从引用返回到元素.


要*解析*引用 - 找到被引用的声明 - 你调用`PsiReference.resolve()`.
这是很

理解`PsiReference.getElement()`和`PsiReference.resolve()`之间的区别很重要.
前一个方法返回_source_

引用,而后者返回其_target_.
在上面的例子中,对于`message`引用,`getElement()`

将在代码片段的第二行返回`message`标识符,`resolve()`将返回`message`标识符

在第一行(在参数列表内).


解析引用的过程与解析不同,并且不会同时执行.
而且,它是

并不总是成功.
如果IDE中当前打开的代码无法编译,或者在其他情况下,这是正常的

对于`PsiReference.resolve()`来返回`null`,如果你使用引用,你需要能够在你的代码中处理它.


## 贡献参考文献


除了由编程语言的语义定义的引用之外,IntelliJ IDEA还可识别许多引用

这取决于代码中使用的API和框架的语义.
请考虑以下示例:


```java
File f = new File("foo.txt");
```

在这里,“foo.txt”从Java语法的角度来看没有特殊含义 - 它只是一个字符串文字.
然而,

如果您在IntelliJ IDEA中打开此示例,并且如果您在同一目录中有一个名为“foo.txt”的文件,您会注意到

您可以按住Ctrl键并单击“foo.txt”并导航到该文件.
这是有效的,因为IntelliJ IDEA识别出

`new File(...)`的语义和_将reference_contribute到作为参数传递给方法的字符串文字中.


通常,引用可以贡献给没有自己引用的元素,例如字符串文字

和评论.
引用也经常被贡献到非代码文件中,例如XML或JSON.


贡献引用是扩展现有语言的最常用方法之一.
例如,您的插件可以

提供对Java代码的引用,即使Java PSI是平台的一部分而未在您的插件中定义.


要提供您自己的参考资料,请参阅[参考贡献者教程](/tutorials/custom_language_support/reference_contributor.md).


## 带有可选或多个解析结果的引用


在最简单的情况下,引用解析为单个元素,如果解析失败,则表示该引用

代码不正确,IDE需要将其突出显示为错误.
但是,有些情况不同.


第一种情况是*软参考*.
考虑上面的`new File(“foo.txt”)`示例.
如果IntelliJ IDEA找不到

文件“foo.txt”,并不意味着需要突出显示错误 - 可能该文件仅在运行时可用.

这样的引用从`PsiReference.isSoft()`方法返回`true`.


第二种情况是*多变量引用*.
考虑一下JavaScript程序的情况. 
JavaScript是动态的

键入的语言,因此IDE无法始终精确地确定在特定位置调用哪个方法.

为了解决这个问题,它提供了一个可以解析为多个可能元素的引用.

这些引用实现了[`PsiPolyVariantReference`](upsource:///platform/core-api/src/com/intellij/psi/PsiPolyVariantReference.java)接口.


要解析`PsiPolyVariantReference`,可以调用它的`multiResolve()`方法.
该调用返回一个数组

[`ResolveResult`](upsource:///platform/core-api/src/com/intellij/psi/PsiResolveResult.java)对象.
每一个

objects标识PSI元素,并指定结果是否有效.
例如,如果您有多个

Java方法重载和一个参数不匹配任何重载的调用,你会得到

返回所有重载的`ResolveResult`对象,`isValidResult()`将为所有重载返回false.


## 搜索参考文献


如您所知,解析引用意味着从使用到相应的声明.
执行

在相反的方向导航 - 从声明到其用法 - 您需要执行**参考搜索**.


要执行引用搜索,请使用

[`ReferencesSearch`](upsource:///platform/indexing-api/com/intellij/psi/search/searches/ReferencesSearch.java)类.

要执行搜索,您需要指定要搜索的*元素*,以及可选的其他参数

需要搜索引用的范围.
你得到一个* query *对象,它允许你获得所有结果

作为一个数组,或逐个迭代结果.
如果您不需要收集所有结果,则效率更高

使用迭代,因为它允许您在找到所需元素后停止处理.


## 实现参考


上述文档涵盖了访问引用的关键点.
如果您需要创建自己的参考

(如果您正在为现有语言实现自定义语言或参考贡献者),

请参阅[指南](/reference_guide/custom_language_support/references_and_resolve.md)和

[tutorial](/tutorials/custom_language_support/reference_contributor.md)用于实现引用.


