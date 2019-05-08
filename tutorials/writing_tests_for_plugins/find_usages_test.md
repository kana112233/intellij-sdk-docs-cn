---
title: 8. Find Usages Test
---

在这个测试中，我们将检查是否找到了发现用法提供程序

[查找用户提供者](/tutorials/custom_language_support/find_usages_provider.md)

部分

[自定义语言支持教程](/tutorials/custom_language_support_tutorial.md)，

工作正常。


### 8.1。
定义测试数据


创建一个文件* FindUsagesTestData.simple *。


```bash
# You are reading the ".properties" entry.
! The exclamation mark can also mark text as comments.
<caret>website = http://en.wikipedia.org/

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

创建文件* FindUsagesTestData.java *。


```java
public class Test {
    public static void main(String[] args) {
        System.out.println("simple:website");
    }
}
```

### 8.2。
定义测试方法


```java
public void testFindUsages() {
    Collection<UsageInfo> usageInfos = myFixture.testFindUsages("FindUsagesTestData.simple", "FindUsagesTestData.java");
    assertEquals(1, usageInfos.size());
}
```

### 8.3。
运行测试


运行测试并确保它是绿色的。


