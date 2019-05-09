---
title: What is the IntelliJ Platform?
---

_IntelliJ Platform_本身并不是产品,而是提供构建IDE的平台.
它用于为JetBrains产品提供动力,如[IntelliJ IDEA](https://www.jetbrains.com/idea/),[WebStorm](https://www.jetbrains.com/webstorm/),[RubyMine](https://www.jetbrains.com/ruby/),[DataGrip](https://www.jetbrains.com/datagrip/)和[Rider](https://www.jetbrains.com/rider/).
它也是开源的,第三方可以使用它来构建IDE,例如来自Google的[Android Studio](https://developer.android.com/studio/index.html).


_IntelliJ Platform_提供了这些IDE提供丰富语言工具支持所需的所有基础结构.
它提供了一个基于组件驱动,跨平台JVM的应用程序主机,具有高级用户界面工具包,用于创建工具窗口,树视图和列表(支持快速搜索)以及弹出菜单和对话框.


它还包括图像编辑器和全文编辑器,并提供语法突出显示,代码折叠,代码完成和其他富文本编辑功能的抽象实现.


此外,它还包括可插入的API,用于构建通用IDE功能,例如项目模型和构建系统.
它还提供了非常丰富的调试体验的基础结构,具有语言无关的高级断点支持,调用堆栈,监视窗口和表达式评估.


但_IntelliJ Platform_的真正力量来自程序结构接口(PSI).
这是一组功能,可用于解析文件和构建代码的丰富语法和语义模型,以及从此数据构建索引.
这提供了许多功能,从快速导航到文件,类型和符号,到代码完成窗口的内容,查找用法,代码检查和代码重写,快速修复或重构,以及许多其他功能.


_IntelliJ Platform_包括多种语言的解析器和PSI模型,其可组合性意味着可以添加对其他语言的支持.


##插件


构建在_IntelliJ Platform_上的产品是可组合的应用程序,平台负责组件的创建,以及将依赖项注入类中. 
_IntelliJ Platform_完全支持插件,JetBrains托管[插件存储库](https://plugins.jetbrains.com),可用于分发支持一个或多个产品的插件.
也可以托管自己的存储库,并单独分发插件.


插件可以通过多种方式扩展平台,从添加简单的菜单项到添加对完整语言,构建系统和调试器的支持. 
_IntelliJ Platform_中的许多现有功能都是作为插件编写的,可以根据最终产品的需要包含或排除这些插件.
有关详细信息,请参阅[插件](/basics.md)一节.


_IntelliJ Platform_是一个JVM应用程序,主要使用Java和Kotlin编写.
您应该熟悉这些语言和相关工具,以便为基于_IntelliJ Platform_的产品编写插件.
目前,无法以非JVM语言扩展_IntelliJ Platform_.


##开源


_IntelliJ Platform_是开源,在[Apache许可证](upsource:///LICENSE.txt)和[托管在GitHub](https://github.com/JetBrains/intellij-community)下.


虽然本指南将_IntelliJ Platform_作为一个单独的实体引用,但没有“IntelliJ平台”GitHub回购.
相反,该平台被认为与IntelliJ IDEA社区版几乎完全重叠,IntelliJ IDEA社区版是IntelliJ IDEA Ultimate的免费开源版本(上面链接的GitHub repo是[JetBrains/intellij-community](https://github.com/JetBrains/intellij-community)repo).


IntelliJ IDEA Ultimate是IntelliJ IDEA社区版的超集.
它基于社区版,但包括闭源插件([请参阅此功能比较](https://www.jetbrains.com/idea/features/editions_comparison_matrix.html)).
同样,WebStorm和DataGrip等其他产品也基于IntelliJ IDEA Community Edition,但包含一组不同的插件,不包括其他默认插件.


这允许插件针对多个产品,因为每个产品都包含基本功能和IntelliJ IDEA Community Edition仓库中的一系列插件.
这就是我们所说的_IntelliJ Platform_.


通常,基于_IntelliJ Platform_的IDE将包含`intellij-community` repo作为Git子模块,并提供配置来描述来自`intellij-community`的哪些插件,以及哪些自定义插件将构成产品.
这就是IDEA Ultimate团队的工作方式,他们为自定义插件和_IntelliJ Platform_本身贡献代码.


当然,因为_IntelliJ Platform_是开源的,我们也接受[pull requests](https://github.com/JetBrains/intellij-community/pulls)到平台本身,而不仅仅是打开视图源.
使用[YouTrack(使用IDEA项目)](https://youtrack.jetbrains.com/issues/IDEA)管理问题跟踪,如果您希望为该平台做出贡献,通常最好打开一个问题
描述在进行更改之前您希望进行的更改 - 这使团队有机会提供反馈和建议.
更多细节可以在[贡献IntelliJ平台](/basics/platform_contributions.md)一节中找到.


## 骑士


[Rider](https://www.jetbrains.com/rider/)使用_IntelliJ Platform_与其他基于IntelliJ的IDE不同.
它使用_IntelliJ Platform_为C#和.NET IDE提供用户界面,具有标准的IntelliJ编辑器,工具窗口,调试经验等.
它还集成到标准的Find Usages和Search Everywhere UI中,并使用代码完成,语法突出显示等.


但是,它不会为C#文件创建完整的PSI(语法和语义)模型.
相反,它重用[ReSharper](https://www.jetbrains.com/resharper/)来提供语言功能.
所有C#PSI模型以及所有检查和代码重写(例如快速修复和重构)都在ReSharper的命令行版本中运行.
这意味着为Rider创建插件涉及两个部分 - 一个插件,它位于IntelliJ“前端”以显示用户界面,一个插件位于ReSharper“后端”以分析和使用C#PSI.


幸运的是,许多插件可以简单地使用ReSharper后端--Rider负责显示检查和代码完成的结果,并且可以编写许多不需要IntelliJ UI组件的插件.
更多详细信息,请参见“产品特定”部分.


