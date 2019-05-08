---
title: 3. Grammar and Parser
---

### 3.1。
定义令牌类型


在`com.simpleplugin.psi`包中创建一个文件。


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/psi/SimpleTokenType.java %}
```

### 3.2。
定义元素类型


在`com.simpleplugin.psi`包中创建一个文件。


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/psi/SimpleElementType.java %}
```

### 3.3。
定义语法


使用*/com/simpleplugin/Simple.bnf *文件为属性语言定义语法。


```java
{
  parserClass="com.simpleplugin.parser.SimpleParser"

  extends="com.intellij.extapi.psi.ASTWrapperPsiElement"

  psiClassPrefix="Simple"
  psiImplClassSuffix="Impl"
  psiPackage="com.simpleplugin.psi"
  psiImplPackage="com.simpleplugin.psi.impl"

  elementTypeHolderClass="com.simpleplugin.psi.SimpleTypes"
  elementTypeClass="com.simpleplugin.psi.SimpleElementType"
  tokenTypeClass="com.simpleplugin.psi.SimpleTokenType"
}

simpleFile ::= item_*

private item_ ::= (property|COMMENT|CRLF)

property ::= (KEY? SEPARATOR VALUE?) | KEY
```

如您所见，属性文件可以包含属性，注释和换行符。


语法定义了对语言的支持的灵活性。

我们指定某个属性可能具有或不具有密钥和值。

这使IDE仍然可以识别错误定义的属性，并提供相应的代码分析和快速修复。


请注意，上面`elementTypeHolderClass`字段中的`SimpleTypes`类指定了从语法生成的类的名称，此时它不存在。


### 3.4。
生成解析器


现在，当定义语法时，我们可以通过* Simple.bnf *文件中的上下文菜单中的* Generate Parser Code *生成带有PSI类的解析器。

这将在* gen *文件夹中生成解析器和PSI元素。

将此文件夹标记为* Generated Sources Root *并确保编译所有内容都没有错误。


![分析器](IMG/generated_pa​​rser.png)


