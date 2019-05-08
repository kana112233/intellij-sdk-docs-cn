---
title: 7. Folding Test
---

在这个测试中，我们将检查折叠构建器是否实现了

[折叠生成器](/tutorials/custom_language_support/folding_builder.md)

部分

[自定义语言支持教程](/tutorials/custom_language_support_tutorial.md)，

按预期工作。


### 7.1。
定义测试数据


创建文件* FoldingTestData.java *。


```java
public class Test {
    public static void main(String[] args)<fold text=' { '> {
        </fold>System.out.println("<fold text='http://en.wikipedia.org/'>simple:website</fold>");<fold text=' }'>
    }</fold>
}
```

### 7.2。
定义一个测试


```java
public void testFolding() {
    myFixture.configureByFiles("DefaultTestData.simple");
    myFixture.testFolding(getTestDataPath() + "/FoldingTestData.java");
}
```

### 7.3。
运行测试


运行测试并确保它是绿色的。


