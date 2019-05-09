---
title: Plugin Compatibility with IntelliJ Platform Products
---

<! -  TODO链接到sample_plugin文件 - >


所有基于_IntelliJ Platform_的产品,如IntelliJ IDEA,RubyMine,WebStorm等,都针对相同的底层平台API,一些产品共享在平台之上构建的功能,例如Java支持(IntelliJ IDEA)

和Android Studio)或数据库支持(IntelliJ IDEA Ultimate,DataGrip,PhpStorm等).


只要插件指定与其兼容的产品,就可以编写兼容多个产品的插件.
更具体地说,插件必须使用[`plugin.xml`文件](/basics/plugin_structure/plugin_configuration_file.md)中的[<depends>`标签](/basics/plugin_structure/plugin_dependencies.md)来指定,依赖项
在众所周知的_modules_或插件上.


出于依赖性的目的,可以将模块视为内置插件,作为产品的不可移除部分提供.
并非所有产品都定义和声明模块.
例如,PhpStorm没有自己的模块,但产品本身依赖于(并附带)PHP语言插件.
您也可以根据相同的语言插件使您的插件与PhpStorm兼容.


例如:


```xml
<idea-plugin>
  ...
  <depends>com.intellij.modules.lang</depends>
  ...
</idea-plugin>
```

<! -  TODO链接到sample_plugin文件 - >


如果插件在其`plugin.xml`中不包含任何模块依赖关系标签,则它被假定为旧插件并且仅在IntelliJ IDEA中加载.


如果`plugin.xml`包含一个或多个这样的标记,如果产品包含插件所依赖的所有**模块,则加载该插件.


目前,基于IntelliJ平台的所有产品均提供以下模块:


*`com.intellij.modules.platform`

*`com.intellij.modules.lang`

*`com.intellij.modules.vcs`

*`com.intellij.modules.xml`

*`com.intellij.modules.xdebugger`


这意味着插件可以声明对`com.intellij.modules.vcs`的依赖,它可以在任何支持版本控制的产品中工作,并且因为所有产品目前都包含`com.intellij.modules.vcs`模块,这个插件
将适用于所有产品.


这些特定产品中提供以下模块或内置插件:


| Module or built-in plugin            | Product                                                                              |
|--------------------------------------|--------------------------------------------------------------------------------------|
| `com.intellij.modules.java`          | IntelliJ IDEA, Android Studio                                                        |
| `com.intellij.modules.ultimate`      | IntelliJ IDEA Ultimate Edition                                                       |
| `com.intellij.modules.androidstudio` | Android Studio                                                                       |
| `com.intellij.modules.appcode`       | AppCode                                                                              |
| `com.intellij.modules.cidr.lang`     | AppCode, CLion                                                                       |
| `com.intellij.modules.cidr.debugger` | AppCode, CLion, RubyMotion                                                           |
| `com.intellij.modules.clion`         | CLion                                                                                |
| `com.intellij.modules.database`      | IntelliJ IDEA Ultimate Edition, DataGrip, GoLand, PhpStorm, PyCharm, Rider, RubyMine |
| `com.intellij.modules.go`            | GoLand                                                                               |
| `com.intellij.modules.python`        | PyCharm                                                                              |
| `com.intellij.modules.rider`         | Rider                                                                                |
| `com.intellij.modules.ruby`          | RubyMine                                                                             |
| `com.jetbrains.php`                  | PhpStorm (built-in plugin)                                                           |
| `com.intellij.modules.webstorm`      | WebStorm                                                           |

##插件依赖项


如果要为插件添加依赖项,请使用插件名称/ID将“<depends>”标记添加到“plugin.xml”.
例如`JavaScript`或`tslint`.
请注意,目标SDK中默认不包含某些插件,因此您还必须手动将其jar添加到SDKs类路径中,以针对其提供的类进行编译.
确保将插件罐添加到SDK而不是插件,否则罐子将与您的插件捆绑在一起.


##可选的依赖项


您还可以指定可选的依赖项.
如果您的插件适用于所有产品但提供了一些特定于Java的功能,则可以使用如下依赖项标记:


```xml
<depends optional="true" config-file="my-java-features.xml">
  com.intellij.modules.java
</depends>
```

在将插件标记为与所有产品兼容之前,您应该验证它不使用任何特定于IntelliJ IDEA的API.
为此,创建一个指向RubyMine,PyCharm等安装的_IntelliJ Platform_ SDK,针对该SDK编译插件,并验证所有内容是否已编译.
访问[开源许可页面](https://www.jetbrains.com/buy/opensource/),检查您的项目是否有资格获得JetBrains产品的免费开源许可.


[JetBrains插件存储库](https://plugins.jetbrains.com/)根据上述规则自动检测与插件兼容的产品,并将其提供给这些产品的用户.


##平台兼容性


_IntelliJ Platform_和捆绑插件的API可以在发行版之间更改.
可能会破坏插件的主要更改列在[IntelliJ平台和插件API中的不兼容更改](/reference_guide/api_changes_list.md)页面上.


为了确保您的插件能够在您的目标平台上运行,您应该使用与您所定位的IntelliJ平台相同的开发版本.


