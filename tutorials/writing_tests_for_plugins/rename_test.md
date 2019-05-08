---
title: 6. Rename Test
---

在这个测试中，我们将检查是否进行了就地重命名

[参考文献贡献者](/tutorials/custom_language_support/reference_contributor.md)

部分

[自定义语言支持教程](/tutorials/custom_language_support_tutorial.md)，按预期工作。


### 6.1。
定义输入测试数据


创建一个文件* RenameTestData.simple *。


```bash
# You are reading the ".properties" entry.
! The exclamation mark can also mark text as comments.
website = http://en.wikipedia.org/

language = English
# The backslash below tells the application to continue reading
# the value onto the next line.
message = Welcome to \
          Wikipedia!
# Add spaces to the key
key\ with\ spaces = This is the value that could be looked up with the key "key with spaces".
# Unicode
tab : \u0009
```

创建一个文件* RenameTestData.java *。


```java
public class Test {
    public static void main(String[] args) {
        System.out.println("simple:website<caret>");
    }
}
```

### 6.2。
创建输出测试数据


创建一个文件* RenameTestDataAfter.simple *。


```bash
# You are reading the ".properties" entry.
! The exclamation mark can also mark text as comments.
websiteUrl = http://en.wikipedia.org/

language = English
# The backslash below tells the application to continue reading
# the value onto the next line.
message = Welcome to \
          Wikipedia!
# Add spaces to the key
key\ with\ spaces = This is the value that could be looked up with the key "key with spaces".
# Unicode
tab : \u0009
```

### 6.3。
定义测试方法


```java
public void testRename() {
    myFixture.configureByFiles("RenameTestData.java", "RenameTestData.simple");
    myFixture.renameElementAtCaret("websiteUrl");
    myFixture.checkResultByFile("RenameTestData.simple", "RenameTestDataAfter.simple", false);
}
```

### 6.4。
运行测试


运行测试并确保它是绿色的。


