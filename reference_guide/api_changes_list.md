---
title: Incompatible Changes in IntelliJ Platform and Plugins API
---

<! - 

在记录API更改之前,请确保无法避免更改

以下文档中的另一种方式说明:


API兼容性政策:https://confluence.jetbrains.com/display/IDEA/IntelliJ+Platform+API+compatibility+policy

API兼容性常见问题解答:https://confluence.jetbrains.com/display/IDEA/IntelliJ+API+Compatibility+Policy+FAQ


标有ApiStatus.@ Experimental的地方不需要记录.


要记录新的不兼容更改,请添加带有问题模式的新行

然后是第二行,带有“:” - 前缀的人类可读描述和推荐的修复/操作.


支持以下问题模式:


<package name>包已删除

删除了<class name>类

<class name>重命名为<new class name>


<类名>.<方法名称>(<人类可读参数>)方法已删除

<类名>.<方法名称>(<人类可读参数>)方法返回类型从<before>更改为<after>

<类名>.<方法名称>(<人类可读参数>)方法参数<type>已删除

<class name>.<方法名称>(<人类可读参数>)方法参数类型从<before>更改为<after>

<class name>.<方法名称>(<人类可读参数>)方法可见性从<before>更改为<after>

<class name>.<方法名称>(<human-readable parameters>)方法标记为final


删除了<class name>(<human-readable parameters>)构造函数

删除<class name>(<human-readable parameters>)构造函数参数<type>

<class name>(<human-readable parameters>)构造函数参数类型已从<before>更改为<after>

<class name>(<human-readable parameters>)构造函数可见性从<before>更改为<after>


删除了<class name>.<field name>字段

<class name>.<field name>字段类型从<before>更改为<after>

<class name>.<field name>字段可见性从<before>更改为<after>


<类名>.<方法名称>(<人类可读参数>)添加了抽象方法

<class name>类移动到包<package name>


其中<class name>是该类的完全限定名称,例如
com.intellij.openapi.actionSystem.AnAction $将InnerClass.

<method name>是确切方法的名称.
请注意,构造函数具有专用模式.

<human-readable parameters>是表示参数的字符串,不一定是完全限定的.
它们不会影响解析器.
例如,您可以自由编写(Object,List <String>,int)而不是(java.lang.Object,java.util.List,int).


注意:如果您尝试记录的更改与上述任何模式都不匹配,请在YouTrack中填写故障单.

票证的一个例子是https://youtrack.jetbrains.com/issue/PR-1218.
在支持之前,您可以根据需要记录更改,稍后我会更正.


注意:您可以使用markdown-features对图案进行美化:
 
1)代码引号:`org.example.Foo.methodName`
 
2)links [org.example.Foo](upsource:///platform/core-api/src/org/example/Foo)
 
3)代码引号和链接:[`org.example.Foo`](upsource:///platform/core-api/src/org/example/Foo)

 - >


在版本之间偶尔会更改IntelliJ API,导致现有插件与较新的IDE版本不兼容.


使用内置[插件验证程序](https://blog.jetbrains.com/platform/2018/)可以轻松验证[JetBrains插件存储库](../plugin_repository/index.md)上托管的插件与新IDE的兼容性
07 /插件存储库,现已集成与 - 的 - 插件验证工具/).
  

以下页面列出了IDE版本中的重大更改以及插件作者需要采取的建议步骤.


* [** 2019年的变化.***](api_changes/api_changes_list_2019.md)

* [** 2018年的变化.***](api_changes/api_changes_list_2018.md)

* [** 2017年的变化.***](api_changes/api_changes_list_2017.md)

* [** 2016年的变化.***](api_changes/api_changes_list_2016.md)


> **注意**未列出标有[`org.jetbrains.annotations.ApiStatus.@ Experimental`]的API的更改(upsource:///platform/util/src/org/jetbrains/annotations/ApiStatus.java)
在这里,因为预期会出现不兼容的变化.


