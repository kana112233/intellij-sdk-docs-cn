---
title: Modifying the PSI
---

PSI是源代码的读写表示,作为对应于源结构的元素树文件.您可以通过*添加*,*替换*和*删除* PSI元素来修改PSI.


要执行这些操作,可以使用诸如`PsiElement.add()`,`PsiElement.delete()`和`PsiElement.replace()`之类的方法,
以及在`PsiElement`接口中定义的其他方法,它允许您在一个单元中处理多个元素操作,或指定树中需要添加元素的确切位置.


就像文档操作一样,PSI修改需要包含在写入操作和命令中(因此只能在事件派发线程中执行).
参见[文件文章](documents.md#the-are-the-rules-of-working-with-documents)

有关命令和写入操作的更多信息.


## 创建新的PSI


要添加到树中或替换现有PSI元素的PSI元素通常是从文本*创建的.
在最常见的情况下,使用[`PsiFileFactory`]的`createFileFromText`方法(upsource:///platform/core-api/src/com/intellij/psi/PsiFileFactory.java)
创建一个新文件,其中包含您需要添加到树中或用作替换的代码构造对于现有元素,遍历生成的树以找到所需的特定元素,然后传递它元素到`add()`或`replace()`.


大多数语言都提供工厂方法,使您可以更轻松地创建特定的代码构造.
例如,

[`PsiJavaParserFacade`](upsource:///java/java-psi-api/src/com/intellij/psi/PsiJavaParserFacade.java)类

包含诸如`createMethodFromText`之类的方法,它从给定文本创建Java方法.


当您实现与现有代码一起使用的重构,意图或检查quickfix时,文本你传递给各种`createFromText`方法将结合硬编码片段和从中获取的代码片段现有文件.
对于小代码片段(单个标识符),您只需附加现有文本即可代码到您正在构建的代码片段的文本.
在这种情况下,您需要确保生成的文本是语法正确,否则`createFromText`方法将抛出异常.
对于较大的代码片段,最好分几步执行修改:


* 从文本创建替换树片段,留下用户代码片段的占位符;
 
* 用用户代码片段替换占位符;
 
* 用替换树替换原始源文件中的元素.


这可确保保留用户代码的格式,并且修改不会引入任何不需要的内容空白变化.


作为此方法的示例,请参阅"ComparingReferencesInspection”示例中的quickfix:


```java
// binaryExpression holds a PSI expression of the form "x == y", which needs to be replaced with "x.equals(y)"
PsiBinaryExpression binaryExpression = (PsiBinaryExpression) descriptor.getPsiElement();
IElementType opSign = binaryExpression.getOperationTokenType();
PsiExpression lExpr = binaryExpression.getLOperand();
PsiExpression rExpr = binaryExpression.getROperand();

// Step 1: Create a replacement fragment from text, with "a" and "b" as placeholders
PsiElementFactory factory = JavaPsiFacade.getInstance(project).getElementFactory();
PsiMethodCallExpression equalsCall =
    (PsiMethodCallExpression) factory.createExpressionFromText("a.equals(b)", null);

// Step 2: replace "a" and "b" with elements from the original file
equalsCall.getMethodExpression().getQualifierExpression().replace(lExpr);
equalsCall.getArgumentList().getExpressions()[0].replace(rExpr);

// Step 3: replace a larger element in the original file with the replacement tree
PsiExpression result = (PsiExpression) binaryExpression.replace(equalsCall);
```

就像IntelliJ Platform API中的其他地方一样,文本传递给`createFileFromText`和其他`createFromText`

方法必须只使用`\n`作为行分隔符.


## 维护树结构一致性


PSI修改方法不会限制您构建结果树结构的方式.
例如,

使用Java类时,可以添加`for`语句作为`PsiMethod`元素的直接子元素,即使

Java解析器永远不会产生这样的结构(`for`语句将始终是`PsiCodeBlock`的子句)

代表方法体).
产生错误树结构的修改似乎可行,但它们会起作用

以后会导致问题和例外.
因此,您始终需要确保使用PSI构建的结构修改操作与解析您构建的代码时解析器生成的操作相同.


为了确保不引入不一致,可以在测试中调用`PsiTestUtil.checkFileStructure()`
你修改PSI的动作.
此方法可确保您构建的结构与解析器生成的结构相同.


## 空白和进口


使用PSI修改函数时,永远不应创建单独的空白节点(空格或换行符)来自文字.相反,所有空格修改都由格式化程序执行,格式化程序遵循代码样式设置

由用户选择.
格式化在每个命令结束时自动执行,如果需要,可以

也可以使用`中的`reformat(PsiElement)`方法手动执行它

[`CodeStyleManager`](upsource:///platform/core-api/src/com/intellij/psi/codeStyle/CodeStyleManager.java)类.


此外,在使用Java代码(或使用类似导入机制(如Groovy或Python)的其他语言中的代码时),

你永远不应该手动创建导入.
相反,您应该将完全限定的名称插入到您的代码中

生成,然后在中调用`shortenClassReferences()`方法

[`JavaCodeStyleManager`](upsource:///java/java-psi-api/src/com/intellij/psi/codeStyle/JavaCodeStyleManager.java)

(或您正在使用的语言的等效API).
这可以确保根据导入创建

用户的代码样式设置并插入到文件的正确位置.


## 结合PSI和文档修改


在某些情况下,您需要执行PSI修改,然后对您刚才的文档执行操作

通过PSI修改(例如,启动实时模板).
在这种情况下,您需要调用一个特殊的方法

完成基于PSI的后处理(例如格式化)并将更改提交到文档.
方法

你需要调用的是`doPostponedOperationsAndUnblockDocument`,它的定义是

[`PsiDocumentManager`](upsource:///platform/core-api/src/com/intellij/psi/PsiDocumentManager.java)类.