---
title: 5. Formatter Test
---

在这个测试中我们将检查格式化程序是否实现在

[Formatter](/tutorials/custom_language_support/formatter.md)

部分

[Custom Language Support Tutorial](/tutorials/custom_language_support_tutorial.md)

按预期工作。


### 5.1。
定义测试数据


创建一个文件* FormatterTestData.simple *。


```bash
# You are reading the ".properties" entry.
! The exclamation mark can also mark text as comments.
website=http://en.wikipedia.org/

language= English
# The backslash below tells the application to continue reading
# the value onto the next line.
message  =  Welcome to \
          Wikipedia!
# Add spaces to the key
key\ with\ spaces = This is the value that could be looked up with the key "key with spaces".
# Unicode
tab :\u0009
```

### 5.2。
定义测试方法


```java
public void testFormatter() {
    myFixture.configureByFiles("FormatterTestData.simple");
    CodeStyleSettingsManager.getSettings(getProject()).SPACE_AROUND_ASSIGNMENT_OPERATORS = true;
    new WriteCommandAction.Simple(getProject()) {
        @Override
        protected void run() throws Throwable {
            CodeStyleManager.getInstance(getProject()).reformatText(myFixture.getFile(),
                            ContainerUtil.newArrayList(myFixture.getFile().getTextRange()));
        }
    }.execute();
    myFixture.checkResultByFile("DefaultTestData.simple");
}
```

### 5.3。
运行测试


运行测试并确保它是绿色的。


