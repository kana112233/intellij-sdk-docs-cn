---
title: 6. PSI Helpers and Utilities
---

### 6.1。
为生成的PSI元素定义辅助方法


如果我们想在PSI类中使用自定义方法，我们需要单独定义它们并要求Grammar-Kit将它们嵌入到生成的代码中。


让我们用这些辅助方法定义一个实用程序类。


```java
package com.simpleplugin.psi.impl;

import com.intellij.lang.ASTNode;
import com.intellij.navigation.ItemPresentation;
import com.intellij.psi.*;
import com.simpleplugin.SimpleIcons;
import com.simpleplugin.psi.*;
import org.jetbrains.annotations.Nullable;

import javax.swing.*;

public class SimplePsiImplUtil {
  public static String getKey(SimpleProperty element) {
    ASTNode keyNode = element.getNode().findChildByType(SimpleTypes.KEY);
    if (keyNode != null) {
      // IMPORTANT: Convert embedded escaped spaces to simple spaces
      return keyNode.getText().replaceAll("\\\\ ", " ");
    } else {
      return null;
    }
  }

  public static String getValue(SimpleProperty element) {
    ASTNode valueNode = element.getNode().findChildByType(SimpleTypes.VALUE);
    if (valueNode != null) {
      return valueNode.getText();
    } else {
      return null;
    }
  }
}
```

请注意，上面代码中引用的`SimpleProperty`接口是由解析器生成的。
另请注意，`getPresentableText`中使用的`element.getKey`方法将不会被定义 - 它是由解析器生成的方法，我们将在下面修复它。


### 6.2。
更新语法并重新生成解析器


现在我们告诉通过`psiImplUtilClass`属性在语法文件中使用这个实用程序类。


要说明必须使用哪些PSI类的方法，我们为特定规则指定方法。


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

  psiImplUtilClass="com.simpleplugin.psi.impl.SimplePsiImplUtil"
}

simpleFile ::= item_*

private item_ ::= (property|COMMENT|CRLF)

property ::= (KEY? SEPARATOR VALUE?) | KEY {methods=[getKey getValue]}
```

在我们对语法进行更改后，我们可以重新生成解析器和PSI类。


### 6.3。
定义实用程序以搜索属性


现在我们需要一个实用程序类来搜索PSI元素以查找项目中已定义的属性。

我们稍后将在实现代码帮助时使用此实用程序。


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleUtil.java %}
```


