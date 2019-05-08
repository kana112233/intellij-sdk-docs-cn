---
title: 8. Line Marker Provider
---

线标记有助于使用装订线上的图标注释任何代码。

这些图标可以提供相关代码的导航。


### 8.1。
定义线标记提供程序


让我们在Java代码中注释我们的属性的用法，并提供对这些属性的定义的导航。


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleLineMarkerProvider.java %}
```

##实施者的更多技术细节


*请返回行标记信息以获取您要求的确切元素。
    
例如，如果为方法调用了`getLineMarkerInfo()`，则不返回类标记信息。


*请尽可能小的元素返回相关的行标记信息。
     
例如，不要为[`PsiMethod`]返回方法标记(upsource:///java/java-psi-api/src/com/intellij/psi/PsiMethod.java)。
而是将其返回给[`PsiIdentifier`](upsource:///java/java-psi-api/src/com/intellij/psi/PsiIdentifier.java)，这是此方法的名称。


###更多技术细节:


当`LineMarkerProvider`为太大的PsiElement返回一些东西时会发生什么？


```java
public class MyLineMarkerProvider implements LineMarkerProvider {
  public LineMarkerInfo getLineMarkerInfo(@NotNull PsiElement element) {
    if (element instanceof PsiMethod) return new LineMarkerInfo(element, ...);
    return null;
  }
}
```

检查(特别是[`LineMarkersPass`](upsource:///platform/lang-impl/src/com/intellij/codeInsight/daemon/impl/LineMarkersPass.java))出于性能原因查询所有[`LineMarkerProviders`](upsource:///platform/lang-api/src/com/intellij/codeInsight/daemon/LineMarkerProviders.java)两次通过:


*首次通过可见区域中的所有元素


*所有其余元素的第二遍


如果提供者不为任何区域返回任何内容，则会清除其线标记。

如果是，例如
一个方法是半可见的(它的名字是可见的，但它的一部分不是)和

一些写得不好的[`LineMarkerProvider`](upsource:///platform/lang-api/src/com/intellij/codeInsight/daemon/LineMarkerProvider.java)返回了'PsiMethod`而不是'PsiIdentifier`的信息，然后:


*第一遍删除行标记信息，因为整个'PsiMethod`不可见。


*第二遍试图添加行标记信息，因为最后为`PsiMethod`调用了'LineMarkerProvider`。


结果，线标记图标会恼人地闪烁。

要解决此问题，请重写`LineMarkerProvider`以返回'PsiIdentifier`而不是'PsiMethod`的信息:


```java
public class MyLineMarkerProvider implements LineMarkerProvider {
  public LineMarkerInfo getLineMarkerInfo(@NotNull PsiElement element) {
    if (element instanceof PsiIdentifier && element.getParent() instanceof PsiMethod) return new LineMarkerInfo(element, ...);
    return null;
  }
}
```

### 8.2。
注册行标记提供程序


```xml
<codeInsight.lineMarkerProvider language="JAVA" implementationClass="com.simpleplugin.SimpleLineMarkerProvider"/>
```

### 8.3。
运行该项目


现在，您可以在装订线上看到图标，并可以导航到属性定义。


！[Line Marker](img/line_marker.png)


