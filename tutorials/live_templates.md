---
title: Live Templates
---

*实时模板*是可自定义的规则，允许开发人员在编辑器中缩写重复的文本模式。
当用户键入指定的缩写后跟可配置的*扩展键*(通常为“Tab”)时，IDE会将前面的输入序列转换为其全长输出，并更新光标位置。
例如，考虑一个`for`循环。
通常，最终用户需要键入`for(int i = 0; i <10; i ++){<Enter> <Tab> <Enter> <Enter>} <Up>`。
这种模式可以缩短为`fori <Tab>`，剩下的内容将被扩展，留下以下结构:


```
for(int i = [|]; i < []; i++) {
    []
}
```
 
 As the user completes each section of the `for` loop and presses `Tab`, the cursor will advance to the next position in the editor. For more information about creating your own Custom Live Templates, you may refer to the [corresponding documentation](https://www.jetbrains.com/idea/help/creating-and-editing-live-templates.html). In this tutorial, we will illustrate how to add default Custom Live Templates to an IntelliJ Platform plugin, and assign valid contexts where these templates can take on added functionality based on the surrounding code and file type. We will discuss how to export existing live templates, and bundle them within a plugin to give users added typing efficiency when using a custom language.
 
 *  [1. Create a new Live Template](live_templates/template_support.md)


