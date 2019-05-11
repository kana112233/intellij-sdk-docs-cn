---
title: Custom Language Support
---

 *IntelliJ Platform* 是一个功能强大的平台,用于构建针对* any *语言的开发工具.

大多数IDE功能由语言无关和语言特定部分组成,您可以通过少量工作为您的语言支持特定功能:

您只需要实现特定于语言的部分,并且平台将为您提供与语言无关的部分.


这部分文档将解释* Language API *的主要概念,并将指导您完成开发自定义语言插件通常所需的一系列步骤.

您可以从* Language API *类的JavaDoc注释以及属性语言支持的源代码中获取有关* Language API *的其他信息,该语言支持是

[IntelliJ IDEA社区版](https://github.com/JetBrains/intellij-community)

源代码.


如果您更喜欢本页提供的详细说明的完整示例,请查看有关如何在“.properties”文件示例中定义自定义语言支持的分步教程:

[自定义语言支持教程](/tutorials/custom_language_support_tutorial.md)


提供自定义语言支持包括以下主要步骤:


* [注册文件类型](/reference_guide/custom_language_support/registering_file_type.md)

* [实施Lexer](/reference_guide/custom_language_support/implementation_lexer.md)

* [实施解析器和PSI](/reference_guide/custom_language_support/implement_parser_and_psi.md)

* [语法突出显示和错误突出显示](/reference_guide/custom_language_support/syntax_highlighting_and_error_highlighting.md)

* [参考文献和解决方案](/reference_guide/custom_language_support/references_and_resolve.md)

* [代码完成](/reference_guide/custom_language_support/code_completion.md)

* [查找用法](/reference_guide/custom_language_support/find_usages.md)

* [重命名重构](/reference_guide/custom_language_support/rename_refactoring.md)

* [安全删除重构](/reference_guide/custom_language_support/safe_delete_refactoring.md)

* [代码格式化程序](/reference_guide/custom_language_support/code_formatting.md)

* [代码检查和意图](/reference_guide/custom_language_support/code_inspections_and_intentions.md)

* [结构视图](/reference_guide/custom_language_support/structure_view.md)

* [环绕声](/reference_guide/custom_language_support/surround_with.md)

* [转到课程并转到符号](/reference_guide/custom_language_support/go_to_class_and_go_to_symbol.md)

* [文档](/reference_guide/custom_language_support/documentation.md)

* [其他次要功能](/reference_guide/custom_language_support/additional_minor_features.md)


请在[插件开发论坛]中提问或提出遗漏主题(https://intellij-support.jetbrains.com/hc/en-us/community/topics/200366979-IntelliJ-IDEA-Open-API-and-Plugin-
发展).


