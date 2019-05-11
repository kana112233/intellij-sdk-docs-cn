---
title: Code Inspections
---

IntelliJ平台提供了专为静态代码分析而设计的工具，称为_code inspections_，它可以帮助用户维护和清理代码，而无需实际执行代码。

自定义代码检查可以作为IntelliJ平台插件实现。

插件方法的示例是IntelliJ Platform SDK代码示例[检查](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/inspection)和[comparison_references_inspection](https://github)
的.com/JetBrains的/的IntelliJ-SDK-文档/树/主/code_samples/comparing_references_inspection)。

此外，comparison_references_inspection代码示例演示了如何实现单元测试。


您还可以通过IntelliJ IDEA用户界面创建自定义检查。

请参阅[代码检查](https://www.jetbrains.com/idea/webhelp/code-inspection.html)和[创建自定义检查](https://www.jetbrains.com/idea/help/creating-custom-inspections.html)了解更多信息。


## 创建一个检查插件


The [comparing_references_inspection](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/comparing_references_inspection) code sample adds a new inspection to the **Java | Probable Bugs** group in the [Inspections list](https://www.jetbrains.com/help/idea/inspections-settings.html).
The inspection reports when the `==` or `!=` operator is used between Java expressions of reference types.  
It illustrates the components for a custom inspection plugin:
* Describing an [inspection](#plugin-configuration-file) in the plugin configuration file. 
* Implementing a [local inspection class](#inspection-implementation-java-class) to inspect Java code in the IntelliJ Platform-based IDE editor.
* Creating a [visitor](#visitor-implementation-class) to traverse the `PsiTree` of the Java file being edited, inspecting for problematic syntax.
* Implementing a [quick fix](#quick-fix-implementation) class to correct syntax problems by altering the `PsiTree` as needed.
  Quick fixes are displayed to the user like [intentions](code_intentions.md).
* Implementing an [inspection preferences panel](#inspection-preferences-panel) to display information about the inspection.
* Writing an HTML [description](#inspection-description) of the inspection for display in the inspection preferences panel.
* Optionally, create a [unit test](#inspection-unit-test) for the plugin.

Although the IntelliJ Platform SDK code samples illustrate implementations of these components, it is often useful to see examples of inspections implemented in the _intellij_community_ code base.
This process can help find inspection descriptions and implementations based on what is visible in the IDE UI.
The overall approach works for inspections aimed at other languages as well. 
* Find an existing inspection that is similar to the one you want to implement in the **Preferences | Editor | Inspections** panel.
  Note the display name of the inspection.
  For example, the Java/Probable Bugs inspection "Object comparison using '==', instead of 'equals()'" is very similar to `comparing_references_inspection`.
* Use the display name text as the [target for a search](https://www.jetbrains.com/help/idea/finding-and-replacing-text-in-project.html) within the _intellij_community_ project.
  This will identify a bundle file if the display name is localized.
  If it is not localized, the search finds either the plugin configuration (`plugin.xml`) file where it is an attribute in the inspection description, or the Java implementation file where it provided by an overridden method.
* In the case of localization, copy the key from the bundle file identified by the search.
  * Use the key text as the target for a search within the _intellij_community_ project.
    This search locates the plugin configuration file that describes the inspection.
  * From the inspection description entry find the `implementationClass` attribute value.
* Use the `implementationClass` text as the [target of a class search](https://www.jetbrains.com/help/idea/searching-everywhere.html#Searching_Everywhere.xml) in the _intellij_community_ codebase to find the Java implementation file.

## 创建检查

[comparison_references_inspection](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/comparing_references_inspection)代码示例报告在Java表达式之间使用`==`或`!=`运算符时
参考类型。

用户可以应用快速修复将`a == b`更改为'a.equals(b)`，或`a!= b`更改为`!a.equals(b)`。
  

`comparison_references_inspection`实现的细节说明了检查插件的组件。


### 插件配置文件

`comparison_references_inspection`在`comparison_references_inspection`插件配置中的`<extensions>`元素中被描述为`<localInspection>`类型([plugin.xml](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/comparison_references_inspection/resources/META-INF/plugin.xml))文件。

在幕后，检查类型被描述为[LangExtensionPoints.xml]中的`<extensionPoint>`(upsource:///platform/platform-resources/src/META-INF/LangExtensionPoints.xml):

*`localInspection`类型用于一次操作一个文件的检查，也可以在用户编辑文件时运行。

*`globalInspection`类型用于跨多个文件操作的检查，相关的修复可能，例如，重构文件之间的代码。

*不推荐使用`inspectionToolProvider`类型，但首选`localInspection`。


最小检查描述必须包含`implementationClass`属性。

如`comparison_references_inspection`插件配置文件所示，可以在`localInspection`元素中定义其他属性，无论是否有本地化。

在大多数情况下，最简单的方法是在插件配置文件中定义属性，因为底层父类根据配置文件描述处理大部分类职责。

请注意，某些属性不会显示给用户，因此它们永远不会本地化。


作为替代方案，检查可以通过覆盖检查实现类中的方法来定义所有属性信息(“implementationClass”除外)。


### 检查实现Java类

Java文件的检查实现，如[`ComparingReferencesInspection`](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/comparing_references_inspection/source/com/intellij/codeInspection/ComparingReferencesInspection.java)，
通常基于Java类[AbstractBaseJavaLocalInspectionTool](upsource:///java/java-analysis-api/src/com/intellij/codeInspection/AbstractBaseJavaLocalInspectionTool.java)。

`AbstractBaseJavaLocalInspectionTool`实现类提供了检查Java类，字段和方法的方法。


更一般地说，`localInspection`类型基于类[`LocalInspectionTool`](upsource:///platform/analysis-api/src/com/intellij/codeInspection/LocalInspectionTool.java)。

检查`LocalInspectionTool`的类层次结构表明IntelliJ平台为各种语言和框架提供了许多子检查类。

其中一个类是新检查实现的良好基础，但定制实现也可以直接基于`LocalInspectionTool`。


检查实施类的主要职责是提供:

* 一个'PsiElementVisitor`对象，用于遍历被检查文件的“PsiTree”。

* 一个`LocalQuickFix`类，用于更改已识别问题的语法。

* 一个`JPanel`将显示在_Inspections_对话框中。


请注意，如果插件配置文件中的检查描述仅定义实现类，则必须通过Java实现中的重写方法提供其他属性信息。


`ComparingReferencesInspection`类定义了两个`String`字段:

* `QUICK_FIX_NAME`定义用户在提示应用快速修复时看到的字符串。

* `CHECKED_CLASSES`包含检查所关注的类名列表。


重写的`ComparingReferencesInspection`方法将在下面的部分中讨论。


### 访客实施班

访问者类评估文件的“PsiTree”的元素是否对检查感兴趣。


`ComparingReferencesInspection.createOptionsPanel()`方法基于[`JavaElementVisitor`](upsource:///java/java-psi-api/src/com/intellij/psi/JavaElementVisitor.java)创建一个匿名访问者类来遍历
正在编辑的Java文件的“PsiTree”，检查可疑语法。

匿名类特别重写了三种方法。

* `visitReferenceExpression()`以防止对引用类型表达式的任何重复访问。

* `visitBinaryExpression()`，它完成了所有繁重的工作。
  
它被称为评估一个'PsiBinaryExpression`，它检查操作数是否是`==`或`!=`，以及操作数是否与此检查相关的类。

* `isCheckedType()`计算操作数的'PsiType`，以确定它们是否对此检查感兴趣。


### 快速修复实施

快速修复类的作用非常类似，允许用户更改检查突出显示的“PsiTree”部分。

当检查突出显示感兴趣的“PsiElement”并且用户选择进行更改时，将调用快速修复。


`ComparingReferencesInspection`实现使用嵌套类`CriQuickFix`来实现基于[`LocalQuickFix`]的快速修复(upsource:///platform/analysis-api/src/com/intellij/codeInspection/LocalQuickFix.java)。

`CriQuickFix`类为用户提供了将`a == b`和`a!= b`表达式分别改为'a.equals(b)`和`!a.equals(b)`的选项。


繁重的工作在`CriQuickFix.applyFix()`中完成，它操纵`PsiTree`来转换表达式。

对“PsiTree”的更改是通过通常的修改方法完成的:

* 获得'PsiElementFactory`。

* 创建一个新的'PsiMethodCallExpression`。

* 将原来的左右操作数替换为新的“PsiMethodCallExpression”。

* 用'PsiMethodCallExpression`替换原始二进制表达式。


### 检查首选项面板

检查首选项面板用于显示有关检查的信息。


由`ComparingReferencesInspection.createOptionsPanel()`创建的面板只定义了一个`JTextField`来显示在`JPanel`中。

当选择`comparison_references_inspection`短名称时，这个`JPanel`被添加到默认的IntelliJ平台_Inspections Preferences_对话框中。

`JTextField`允许在面板中显示时编辑`CHECKED_CLASSES`字段。


请注意，IntelliJ平台提供了_Inspections Preferences_面板中显示的大部分UI。

只要正确定义了检查属性和检查描述，IntelliJ平台就会在_Inspections Preferences_ UI中显示信息。


### 检验说明

检查描述是HTML文件。

从列表中选择检查时，描述将显示在_Inspections Preferences_对话框的右上方面板中。
  

在检查实现的类层次结构中使用[`LocalInspectionTool`](upsource:///platform/analysis-api/src/com/intellij/codeInspection/LocalInspectionTool.java)隐含意味着遵循一些约定。

* 检查描述文件应位于`<plugin root dir>/resources/inspectionDescriptions /`下。
  
如果检查描述文件位于其他位置，则在检查实现类中重写`getDescriptionUrl()`。

* 描述文件的名称应该是检查描述或检查实现类提供的检查`<短名称> .html`。
  
如果插件未提供短名称，则IntelliJ平台会计算一个。
  

### 检查单元测试

`comparison_references_inspection`代码示例为检查提供单元测试。

有关插件测试的一般信息，请参阅[测试插件](/basics/testing_plugins.md)部分。


`comparison_references_inspection`测试基于[`UsefulTestCase`](upsource:///platform/testFramework/src/com/intellij/testFramework/UsefulTestCase.java)类，它是JUnit框架API的一部分。

该类处理许多用于测试的底层样板。


按照惯例，文件夹`<project root>/testSource/testPlugin /`包含测试源代码，必须标记为“Tests”文件夹。

如果不是，则DevKit项目无法找到源代码。


按照惯例，文件夹`<project root>/testData /`包含测试文件，必须标记为“Test Resources”文件夹。

该文件夹包含使用名称约定`* .java`和`* .after.java`的每个测试的文件对。


在“comparison_references_inspection”的情况下，测试文件是`before.java`和`before.after.java`，以及`before1.java`和`before1.after.java`。

`before`和`before1`的选择是任意的。


`comparison_references_inspection`测试运行对`* .java文件`的检查，实现快速修复，并将结果与​​相应的`* .after.java`文件进行比较。



## Running the Comparing References Inspection Code Sample
The [comparing_references_inspection](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/comparing_references_inspection) code sample adds a new inspection to the **Java | Probable Bugs** group in the [Inspections list](https://www.jetbrains.com/help/idea/inspections-settings.html).
The inspection reports when the `==` or `!=` operator is used between Java expressions of reference types.  

要运行示例插件:

* 启动**IntelliJ IDEA**，打开`intellij-sdk-docs`项目，并突出显示[comparison_references_inspection](https://github.com/JetBrains/intellij-sdk-docs/tree/master/code_samples/comparing_references_inspection
)模块。

* 打开[项目结构](https://www.jetbrains.com/help/idea/project-structure-dialog.html)对话框，确保项目设置对您的环境有效。

* 如有必要，修改`comparison_references_inspection`模块的[运行/调试配置](https://www.jetbrains.com/idea/webhelp/run-debug-configuration-plugin.html)。

* 在主菜单上选择** Run **运行插件。


### 配置插件


启动插件后，您可以设置插件选项。

您可以指定参与代码检查的Java类以及找到的可能错误的严重性级别。


On the IDEA main menu, open the **Preferences | Editor | Inspections** dialog. 
In the list of the IntelliJ IDEA _Java_ inspections, expand the _Probable bugs_ node, and then click _SDK: '==' or '!=' instead of 'equals()'_.  

![](IMG/comparingReferences_options.png)


在**选项**下，您可以指定以下插件设置:

* 从** Severity **列表中，选择插件找到的可能错误的严重性级别，例如警告，信息等。

* 在** Severity **下的文本框中，指定以分号分隔的Java类列表以参与此代码检查。

* 完成后，单击**确定**。


### 它是如何工作的？


该插件检查您在IntelliJ IDEA编辑器中打开的代码或您键入的代码。

该插件突出显示了代码片段，其中引用类型的两个变量由`==`或`!=`分隔，并建议用`.equals()`替换此代码片段:


![](IMG/comparingReferences.png)


在这个例子中，`str1`和`str2`是String类型的变量。

单击_SDK:使用equals()_替换:


```java
return (str1==str2);
```

用代码:


```java
return (str1.equals(str2));
```


