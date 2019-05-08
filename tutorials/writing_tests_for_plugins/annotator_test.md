---
title: 4. Annotator Test
---

在这个测试中，我们将检查是否在注释器中实现了

[Annotator](/tutorials/custom_language_support/annotator.md)部分

的

[自定义语言支持教程](/tutorials/custom_language_support_tutorial.md)

按预期工作。


### 4.1。
定义测试数据


创建一个文件* AnnotatorTestData.java *。


```java
public class Test {
    public static void main(String[] args) {
        System.out.println("simple:website");
        System.out.println("simple:<error descr="Unresolved property">websit"</error>);
    }
}
```

### 4.2。
定义测试方法


```java
public void testAnnotator() {
    myFixture.configureByFiles("AnnotatorTestData.java", "DefaultTestData.simple");
    myFixture.checkHighlighting(false, false, true, true);
}
```

### 4.3。
运行测试


运行测试并确保它是绿色的。


