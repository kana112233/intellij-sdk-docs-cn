---
title: Testing Highlighting
---

编写插件测试时的一个常见任务是测试各种突出显示(检查,注释器,解析器错误突出显示等). *IntelliJ Platform* 为此任务提供专用的实用程序和标记格式.


要测试当前加载到内存编辑器中的文件的突出显示,请调用`checkHighlighting()`方法.
该方法的参数指定在将结果与预期结果进行比较时应考虑哪些严重性:始终考虑错误,而警告,弱警告和信息是可选的.
或者,您可以使用`testHighlighting()`方法,该方法将`testdata`文件加载到内存编辑器中,并将其作为单个操作突出显示.


如果您需要测试检查(而不是突出显示词法分析器或注释器提供的通用突出显示),则需要启用您正在测试的检查.
这是通过在测试的setup方法中调用`CodeInsightTestFixture.enableInspections()`或直接在测试方法中调用`checkHighlighting()`来完成的.


突出显示的预期结果直接在源文件中指定.
该平台支持广泛的类似XML的标记语言.
在最简单的形式中,标记看起来像这样:


```xml
<warning descr="expected error message">code to be highlighted</warning>
```

或者,作为一个更具体的例子:


```xml
public int <warning descr="The compareTo() method does not reference 'foo' which is referenced from equals(); inconsistency may result">compareTo</warning>(Simple other) {
    return 0;
}
```

标签名称指定预期突出显示的严重性.
支持以下严重性:


* `<错误>`

* `<警告>`

* `<weak_warning>`

* `<info>`

* `<inject>`(对于注入的片段)

* `<symbolName>`(用于根据类型突出显示标识符的标记)

* 任何自定义严重性都可以通过其名称引用


标签还可以具有以下可选属性:


* `descr`  - 与荧光笔相关的预期消息(如果未指定,任何文本将匹配;如果消息包含引号,则可以通过在其前面放置两个反斜杠字符来转义它)

* `foregroundColor`,`backgroundColor`,`effectColor`用于突出显示的预期颜色

* `effectType`预期效果类型用于突出显示(有关可能的值,请参阅`EffectType`枚举)

* `fontType`预期突出显示的字体样式(0  - 正常,1  - 粗体,2  - 斜体,3  - 粗体斜体)


> **Note** *Nested* tags are **supported**: 
>   ```<warning>warning_highlight<info>warning_and_info_highlight</info>warning_highlight</warning>```
> *Overlapping* tags (annotations) are currently **not supported** in the test framework (but display correctly in the editor, albeit this is not an officially supported scenario): 
>   ```<warning>warning_highlight<info>warning-and_info_highlight</warning>info_highlight</info>```


