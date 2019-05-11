---
title: Publishing a Plugin to a Custom Plugin Repository
---

如果您打算使用插件存储库_ [JetBrains插件存储库](https://plugins.jetbrains.com),

你需要:

* 在用于自定义存储库的HTTPS Web服务器上创建和维护`updatePlugins.xml`文件.  
此文件描述了自定义存储库中可用的所有插件以及每个插件的下载URL.

* 将您的插件JAR/ZIP文件上传到HTTPS Web服务器.
这可以是您用于自定义存储库的同一Web服务器
  
或者不同的HTTPS Web服务器.

* 将自定义存储库的URL添加到JetBrains IDE [存储库设置/首选项](https://www.jetbrains.com/help/idea/managing-plugins.html#repos).


## 在updatePlugins文件中描述您的插件

每个自定义插件存储库必须至少有一个`updatePlugins.xml`文件来描述最新的可用版本

对于每个托管插件. 
JetBrains IDE使用`updatePlugins.xml`中的描述来按属性定位插件

例如id,IDE版本和插件版本. 
JetBrains IDE显示这些属性,以帮助用户选择或升级插件.

该描述还告诉JetBrains IDE在哪里下载插件本身.


自定义插件存储库的`updatePlugins.xml`文件由管理员构建和维护存储库.
如果自定义存储库的使用者使用更多,则可能需要多个`updatePlugins.xml`文件

比一个版本的JetBrains IDE.
例如,对于IntelliJ IDEA 2018.2和2018.3,分别为`updatePlugins-182.xml`,`updatePlugins-183.xml`.

每个`updatePlugins  -  *.xml`文件都有一个添加到JetBrains IDE的唯一URL
[存储库设置/首选项](https://www.jetbrains.com/help/idea/managing-plugins.html#repos).


### updatePlugins文件的格式
`updatePlugins.xml`文件的格式只是描述每个插件的顺序元素列表:


```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- 
  The <plugins> element contains the description of the plugins available at this repository. Required. 
-->
<plugins>
  <!-- 
    Each <plugin> element describes one plugin in the repository. Required.
    id - used by JetBrains IDEs to uniquely identify a plugin. Required. Must match <id> in plugin.xml
    url - path to download the plugin JAR/ZIP file. Required. Must be HTTPS
    version - version of this plugin. Required. Must match <version> in plugin.xml
  -->
  <plugin id="fully.qualified.id.of.this.plugin" url="https://www.mycompany.com/my_repository/mypluginname.jar" version="major.minor.update">
    <!--
      The <idea-version> element must match the same element in plugin.xml. Required.
    -->
    <idea-version since-build="181.3" until-build="191.*" />
  </plugin>
  <plugin id="id.of.different.plugin" url="https://www.otherserver.com/other_repository/differentplugin.jar" version="major.minor">
    <idea-version since-build="181.3" until-build="191.*" />
  </plugin>
  <plugin>
    <!-- And so on for other plugins... -->
  </plugin>
</plugins>
```
<br>
**Note:** 
* An `updatePlugins.xml` file must contain at least one set of `<plugin></plugin>` elements.  
* A plugin `id` may be listed only once in an `updatePlugins.xml` file.  
* Multiple plugins with the same `id` but different `idea-version` attributes must be split into separate `updatePlugins-*.xml` files. 

### 可选的updatePlugin元素

可以在`updatePlugins.xml`中添加其他元素吗？
是的,但仅在需要时才建议.
额外的元素将有

与每个插件的`plugin.xml`文件同步.


在插件安装期间,JetBrains IDE读取插件JAR/ZIP文件,然后显示有关插件的更多信息.
哪些附加信息可能有助于用户在何时选择插件

[浏览自定义插件存储库](https://www.jetbrains.com/help/idea/managing-plugins.html#repos)

安装前？
答案取决于插件和存储库使用者.
以下是候选元素:


| Element                                                      |  Effects & Requirements     |
|:-------------------------------------------------------------|:----------------------------| 
| `<name>`<br>My Plugin Name<br>`</name>`                      | By default the name of the plugin JAR/ZIP file is displayed before installation. <br>Using the `<name>` element displays the name of the plugin. <br>Contents should match the `<name>` element contents in the plugins's `plugin.xml` file to avoid confusion. |
| `<description>`<br>My plugin is awesome<br>`</description>`  | By default no description for the plugin is displayed before installation. <br>Using the `<description>` element will cause a description to be displayed before installation. <br>Contents should match the `<description>` element contents in the plugins's `plugin.xml` file to avoid confusion. <br>Optionally, an enclosing `<![CDATA[ ]]>` element can be used if the description needs to contain HTML tags. |
| `<change-notes>`<br>Added cool feature<br>`</change-notes>`  | By default no change notes for the plugin are displayed before installation. <br>Using the `<change-notes>` element will cause a description of changes to be displayed before installation. <br>Contents should match the `<change-notes>` element contents in the plugin's `plugin.xml` file to avoid confusion. <br>Optionally, an enclosing `<![CDATA[ ]]>` element can be used if the change notes need to contain HTML tags. |

