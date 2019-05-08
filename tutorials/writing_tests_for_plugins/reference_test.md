---
title: 10. Reference Test
---

在这个测试中，我们将检查是否实现了引用

[参考文献贡献者](/tutorials/custom_language_support/reference_contributor.md)

部分

[自定义语言支持教程](/tutorials/custom_language_support_tutorial.md)，

按预期工作。


### 10.1。
定义测试数据


创建一个文件* ReferenceTestData.java *。


```java
public class Test {
    public static void main(String[] args) {
        System.out.println("simple:website<caret>");
    }
}
```

### 10.2。
定义测试方法


```java
public void testReference() {
    myFixture.configureByFiles("ReferenceTestData.java", "DefaultTestData.simple");
    PsiElement element = myFixture.getFile().findElementAt(myFixture.getCaretOffset()).getParent();
    assertEquals("http://en.wikipedia.org/", ((SimpleProperty) element.getReferences()[0].resolve()).getValue());
}
```

### 10.3。
运行测试


运行测试并确保它是绿色的。


