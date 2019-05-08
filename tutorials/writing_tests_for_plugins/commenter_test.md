---
title: 9. Commenter Test
---

在此测试中，我们将检查在[自定义语言支持教程](/tutorials/custom_language_support_tutorial.md)的[Commenter](/tutorials/custom_language_support/commenter.md)部分中实现的评论者是否按预期工作。


### 9.1。
定义测试方法


```java
public void testCommenter() {
    myFixture.configureByText(SimpleFileType.INSTANCE, "<caret>website = http://en.wikipedia.org/");
    CommentByLineCommentAction commentAction = new CommentByLineCommentAction();
    commentAction.actionPerformedImpl(getProject(), myFixture.getEditor());
    myFixture.checkResult("#website = http://en.wikipedia.org/");
    commentAction.actionPerformedImpl(getProject(), myFixture.getEditor());
    myFixture.checkResult("website = http://en.wikipedia.org/");
}
```

### 9.2。
运行测试


运行测试并确保它是绿色的。


