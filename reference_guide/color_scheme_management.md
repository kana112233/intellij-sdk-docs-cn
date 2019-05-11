---
title: Color Scheme Management in IntelliJ IDEA 12.1+
---

#  前言


IntelliJ IDEA 12.1中的配色方案管理已经改变,以简化方案设计人员的工作,并使方案看起来同样适用于不同的编程语言,即使不是专门为这些语言设计的.
以前语言插件使用不兼容的固定默认颜色,例如黑暗方案.
新实现允许指定对一组标准文本属性的依赖性,这些属性链接到方案但不链接到任何特定语言.
如果需要,语言特定的属性仍然可以由方案设计者设置,但它是可选的.
新的配色方案有了新的.icls(Idea CoLor Scheme)扩展,以避免混淆与旧平台版本的兼容性问题:

如果仅设置了标准属性,则12.1之前的版本将不会使用它们,这将导致不同的突出显示颜色.


# 插件开发人员


##文本属性键依赖项


指定突出显示文本属性的最简单和最佳方法是指定对* DefaultLanguageHighlighterColors *类中定义的标准键之一的依赖关系.

这是一个例子:


```java
static final TextAttributesKey MY_KEYWORD = TextAttributesKey.createTextAttributesKey("MY_KEYWORD", DefaultLanguageHighlighterColors.KEYWORD);
```

颜色方案管理器将首先搜索由“MY_KEYWORD”键指定的文本属性.

如果没有明确定义或者所有属性都为空(未定义),它将使用“DEFAULT_KEYWORD”键搜索它们.

如果两者都没有定义,它将进一步回退到默认方案.

可以链接文本属性键,例如,您可以将另一个键定义为:


```java
static final TextAttributesKey MY_PREDEFINED_SYMBOL = TextAttributesKey.createTextAttributesKey("MY_PREDEFINED_SYMBOL", MY_KEYWORD);
```

规则是相同的:如果“MY_PREDEFINED_SYMBOL”键无法找到文本属性或为空,则配色方案管理器将搜索“MY_KEYWORD”,如果未找到(空),则将进一步查找“DEFAULT_KEYWORD”.

*注意:*现在非常不鼓励使用固定的默认属性.

如果您不确定使用哪个基本密钥,最好选择最通用的密钥,例如* DefaultLanguageHighlighterColors.IDENTIFIER *.

请记住,使用固定的默认属性*将强制*方案设计者明确地为此元素设置颜色.

否则,它的默认颜色可能在视觉上与配色方案冲突.

如果方案设计者没有语言插件,他根本无法解决这个问题.


##为特定方案提供属性


语言插件可以为“默认”和“Darcula”捆绑方案提供默认文本属性,或者如果方案名称已知则基本上为任何其他方案提供默认文本属性.

这可以在* plugin.xml *中完成,方法是添加一个扩展名,其中包含包含所需文本属性的文件名,例如:


```xml
<extension ...>
..
  <additionalTextAttributes scheme="Default" file="colorSchemes/MyLangDefault.xml"/>
..
</extension>
```

它告诉IDE必须在“colorSchemes”下的资源中搜索文件* MyLangDefault.xml *.

请注意,路径应该*不*以反斜杠开头,并且其完全限定名称(在我们的示例中为* colorSchemes/MyLangDefault.xml *)*必须是唯一*以避免命名不同提供者之间的冲突.

因此,建议添加语言前缀,例如“MyLang”.

文件本身是具有必需属性的颜色方案的摘录,例如:


```xml
<?xml version='1.0'?>
<list>
  <option name="MY_VAR">
    <value>
      <option name="FOREGROUND" value="660000"/>
    </value>
  </option>
  <option name="MY_SPECIAL_CHAR">
    <value>
      <option name="FOREGROUND" value="008000"/>
      <option name="BACKGROUND" value="e3fcff"/>
      <option name="FONT_TYPE" value="1"/>
    </value>
  </option>
</list>
```

*注意:*当通过“另存为...”复制方案时,其所有属性(包括扩展中定义的属性)也将被复制到新方案中.

方案设计者可能需要检查这些复制的属性是否与他/她的配色方案不冲突,尽管在这种情况下安装了插件并且它不应该导致任何问题.

无论如何,如果可能的话,尝试坚持使用简单的密钥依赖(注意它也适用于“Darcula”),只有在真正需要时才提供显式属性.


#Program Designers


##新方案创建的典型工作流程


*选择将用作基础的方案,例如“默认”


*单击“另存为...”并为新方案命名


*首先在* General *部分设置属性并继续*语言默认值*


*检查所有语言并根据需要调整特定于语言的文本属性.

在大多数情况下,这可能不需要,但有两种情况可能需要额外的行动:


*有一个过时的插件,它不使用新的配色方案管理API,因此不使用“语言默认值”中设置的属性.
    
理想情况下,必须为语言插件创建一个报告,以便其作者最终修复它.


*插件有意设置一些默认颜色,如果方案是从默认颜色创建的,则将颜色复制到新创建的方案中.
    
这可以通过重置所有属性来修复,以恢复语言默认值(见下文)的继承,或者通过设置适合该方案的其他颜色.
    
第一种方式是优选的,因为稍后需要更少的努力来改变配色方案.


##文本属性继承


对于没有任何值的许多语言文本属性,将有一行指示属性是从某个部分/属性继承的,例如:“关键字(语言默认值)”.

如果元素设置了* any *属性,则仅使用这些属性,将忽略基本元素中的所有属性.

要*恢复*继承,请取消选中所有框,然后单击*应用*.


