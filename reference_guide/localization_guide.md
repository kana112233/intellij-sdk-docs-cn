---
title: Localization Guide
---

本文档的目的是描述创建IDEA本地化版本所需的步骤.


##应用程序包布局


关于本地化目的,需要翻译的所有资源(英文)都位于调用的jar文件中

*** *** resources_en.jar.

IDEA核心功能有一个这样的jar文件位于

***％INST_HOME％\ LIB \ resources_en.jar ***

和每个捆绑插件的一个罐子

***％INST_HOME％\插件\ $插件$ \ LIB \ resources_en.jar ***.


翻译的资源应该是震撼的,并且完全放在原始jar来自的相同文件夹中.

因此,本地化包应该具有完全相同数量的jar文件,并且它们必须以与原始jar布局完全相同的方式布局.

为了在没有本地化包相互覆盖的情况下为每个安装启用多个本地化,我们建议在jar名称中包含语言环境的名称(例如,*** resources_ja.jar ***).


## resources_en.jar的内容和布局


属性文件通常包含消息,菜单项,对话框标签文本等.

对于每个这样的文件,本地化jar应该包含翻译版本,该版本放置在相对于jar根目录的完全相同的路径上,并且与原始文件和区域设置标识符具有完全相同的名称.

例如来自*** resources_en.jar ***的***消息/ActionsBundle.properties ***文件应该在*** resources_ja.jar中有其翻译版***消息/ActionsBundle_ja.properties ***文件** 
*.

所有属性文件都应使用* \ uXXXX *序列进行ASCII编码,这些序列用于在ASCII范围内没有表示的字符.

看到

[native2ascii的](https://docs.oracle.com/javase/7/docs/technotes/tools/solaris/native2ascii.html)

工具了解更多细节.


属性值主要遵循MessageFormat规则.


> **注意**由于历史原因,主菜单,工具栏,弹出菜单和其他操作的助记符字符以`\ _`(下划线)字符为前缀,而所有其他助记符(如复选框,按钮等)使用`＆` 
(＆符号)签署相同的目的.
此外,在某些地方可能会遇到`&&`(双＆符号),它表示在MacOS X下使用的替代助记符(映射到'U`,`I`,`O`,`N`字符的助记符在那里不起作用
).
通常,使用原始属性值中使用的相同助记符表示,一切都会正常.


##组件位置


*  **Inspection descriptions**  appear in Settings|Errors and represent short information about what each of the inspection tools is intended to do.
Each description is represented by single html file under ***/inspectionDescriptions/*** folder that should be encoded in UTF-8 encoding.
Localized versions should be stored in folder suffixed with locale instead. For instance ***/inspectionDescriptions/CanBeFinal.html*** from ***resources_en.jar*** translation should be placed in ***/inspectionDescriptions_ja/CanBeFinal.html*** in ***resources_ja.jar***.

* **意图描述和样本**与检查描述非常相似,但布局更加先进.

每个意图都有一堆文件位于/intentionDescriptions /中以意图的短名称命名的文件夹中.

这些文件包括description.html,其中包含类似于检查的描述以及一些模板文件,这些文件证明了对样本的意图.

这些模板是可选的翻译.
与检查描述类似,整个intenDescriptions文件夹应以区域设置标识符为后缀.

例如***/intentionDescriptions/AddOnDemandStaticImportAction/description.html ***翻译应放在***/intentionDescriptions_ja/AddOnDemandStaticImportAction/description.html ***中.

所有HTML文件都应该是UTF-8编码的.


* **当天的提示**遵循相同的模式检查和意图.

例如,***/tips/AdaptiveWelcome.html ***的翻译转到***/tips_ja/AdaptiveWelcome.html ***.

关于提示的唯一特别之处是它们使用特殊模式来表示像* EnterAction *这样的快捷方式;

将在运行时替换为当前使用的keymap中映射到* EnterAction *的击键.

所以请确保在翻译时保持这些序列完好无损.

记住UTF-8编码.


* **文件模板**再次以相同的方式(如果应该翻译).

***/fileTemplates/Singleton.java.ft ***转到***/fileTemplates_ja/Singleton.java.ft ***.


在本地化版本中找不到某些资源时,遵循Sun规则的属性包,将使用*** resources_en.jar ***的默认版本.


