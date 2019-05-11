---
title: Style Guide for SDK Documents
---

本文档描述了在创作开源IntelliJ Platform SDK文档时使用的书写风格.
在开始之前,请仔细阅读本页以及[行为准则](/CODE_OF_CONDUCT.md)和[许可证](https://github.com/JetBrains/intellij-sdk-docs/blob/master/LICENSE.txt)文件.
有关为IntelliJ平台本身做出贡献的信息,请访问[贡献IntelliJ平台](/basics/platform_contributions.md).


首先,我们应该牢记我们的观众和他们的目标:
_有人阅读技术内容通常希望回答具体问题.
这个问题可能是广泛的或狭隘的,但不管怎样,我们的目标是提供答案而不会分心._


使用名为[_Markdown_](https://github.github.com/gfm/)的标记语言捕获_Intellij Platform SDK_文档的样式.
以下部分根据Markdown格式描述SDK文档样式:
* [文档标记样式](#documentation-markup-style)
    * [页面格式](#page-format)
    * [重定向](#reireirects)
    * [页面的目录](#page-of-contents-for-a-page)
    * [文本格式约定](#text-format-conventions)
    * [液体标签和过滤器](#liquid-tags-and-filters)
    * [语法高亮](#syntax-highlighting)
    * [表](#tables)
    * [链接](#links)
    * [标注](#notes-and-callouts)
    * [图片](#images)
* [_SUMMARY.md网站目录](#summary-site-of-contents)
* [Repository Submodules](#repository-submodules)


## 文档标记样式
默认情况下,在构建站点时,所有文件都将复制到目标`_site`文件夹.
一些文件被排除在`_config.yml`和`sdkdocs-template/jekyll/_config-defaults.yml`文件中.
文档文件本身是[Markdown](https://github.github.com/gfm/)文件(`*.md`),在构建网站时会自动转换为HTML.


### 页面格式
只有以[YAML](https://yaml.org)标头开头的Markdown文件才会转换为HTML.
如果Markdown文件不包含标题,则不会转换它.
换句话说,要将`.md`文件转换为HTML,它应如下所示:

```yaml
---
---

Lorem ipsum...
```

文件顶部的两行是YAML _front matter_的标记.
可以在这些标记之间添加字段,并在生成HTML时使用.
通常,此标头为空,尽管Jekyll需要它(如果省略,则不转换文件).

YAML标头可以包含生成站点时使用的数据.
例如,页面标题可以指定为Markdown的简单部分 - `# Title`,或者可以在YAML中指定,页面模板可以适当地显示它:

```yaml
---
title: The Title Of The Page
---

Lorem ipsum...
```

YAML标头还可以包括[重定向](#redirects)信息.

Markdown文件中标题周围的行间距通常不会影响HTML转换,但它确实使MD页面对作者更具可读性:
* 标题与其下方的第一个内容之间没有空格.
* 如果是前一部分的同一级别或子标题,则前一个空格.
* 标题之前的两个空格,其高于上一节的标题.

### 重定向
文档站点设置为包含[jekyll-redirect-from](https://github.com/jekyll/jekyll-redirect-from)插件,该插件将生成自动重定向到给定页面的“虚拟”页面.
例如,要指定生成`index.html`页面以重定向到`README.md`,`README.md`文件应在YAML头中包含以下内容:


```yaml
---
redirect_from:
  - /index.html
---

Lorem ipsum...
```

重定向将创建一个`index.html`文件,该文件将自动重定向到生成的`README.html`文件.
重定向使站点URL能够自动显示`README.html`文件 - `http://localhost:4001/foo-test/`将尝试加载`index.html`,它将自动重定向到`README.html` .


重命名或移动文件时重定向也很有用.
可以将多个重定向添加到YAML标头中.

### 页面的目录
该网站配置为使用[Kramdown Markdown转换器](http://kramdown.gettalong.org),它增加了传统Markdown的一些额外功能.
例如,“属性列表”可以将属性应用于生成的元素.

一个有用的属性是`{:toc}`,它可以应用于列表项,它将被替换为标题项的链接列表.
例如.
以下列表项将替换为页面中所有标题项的链接:

```md
  * Dummy list item
  {:toc}
```

进一步的Kramdown功能在[转换器页面](https://kramdown.gettalong.org/converter/html.html)中进行了描述,属性列表在[语法页面]中进行了描述(http://kramdown.gettalong.org/syntax.html).
请注意,源代码格式配置为使用[GitHub Flavored Mardown](https://help.github.com/articles/github-flavored-markdown/)和“code fences”,如下所示.


### 液体标签和过滤器
Jekyll使用[Liquid](https://shopify.github.io/liquid/)模板语言来处理文件.
此过程意味着可以使用标准的液体标签和过滤器.
然而,应该没有必要使用它们,因为Markdown格式已经相当丰富.
有关详细信息,请参阅[Jekyll网站](http://jekyllrb.com/docs/templates/).


### Text Format Conventions
Consistent text styles are used to standardize references and keywords:
* Menu paths are formatted as bold with pipe characters separating each level: \*\*Preferences \\\| Editor\*\* (**Preferences \| Editor**)
* Keywords and quotations are formatted as italic: \_UI Theme\_ (_UI Theme_)
* File names and paths, in-paragraph code fragments, and language keywords are formatted as code: \`interface\` (`interface`),
* File formats are shown as all capital letters: PNG and XML.
* File name extensions are not capitalized when part of a full file name, path, or URL: `filename.ext`.
* [Links](#links) have a particular format depending on the type of link.

一致的术语有助于读者更快地掌握新概念:
* GitHub存储库`intellij-community`中的开源代码称为_IntelliJ Platform_.
  使用SDK文档中的完整短语.
* 基于IntelliJ平台的IDE被描述为_IntelliJ基于平台的IDEs_.
  一旦在页面上使用该术语,之后作者可以使用IDE
* 在提及JetBrains产品时,请始终使用全名,例如_IntelliJ IDEA_.

### 语法高亮显示
源代码可以使用[GitHub Flavored Markdown](https://help.github.com/articles/github-flavored-markdown/)代码围栏来表示,这是三个反引号:

    ```
    // Source code goes here...
    ```

可以通过在第一组滴答之后指定语言来应用语法突出显示:


    ```csharp
    // Some C# code
    ```

    ```java
    // Some Java code
    ```

以下列表是[支持的语言](https://github.com/jneen/rouge/wiki/List-of-supported-languages-and-lexers),以及[Kotlin](https://kotlinlang.org), 当然.

请保持代码示例简洁,避免任何不必要的“周围”代码或import语句.

<!-- //TODO:目前不支持rouge或网站的CSS

该站点还配置为突出显示源代码中的一系列文件,方法是指定`{start-end}`,它是突出显示的开始和结束行:

    ```java{2-3}
    // Not highlighted
    // Highlighted
    // Highlighted
    // Not highlighted
    ```
-->

### Tables
The Kramdown parser also supports tables.
The syntax is to use the pipe (`|`) and minus symbols:

    ```md
    | Column 1 | Column 2 | Column 3 |
    |----------|----------|----------|
    | Blah     | Blah     | Blah     |
    ```

### 链接
链接作为普通的Markdown链接处理,可以锚定到外部站点,站点内的页面或站点中的标题.
将Markdown标头转换为HTML标头时,会为其分配一个ID,以便进行链接.
例如,`## Introduction`将获得`introduction`的ID,并且可以在同一页面或跨页面中链接,如下所述.

IntelliJ平台(`intellij-community`)存储库中文件的链接使用`upsource:///`而不是存储库的完整URL.
`upsource:///`URI有效地指向`intellij-community`存储库的根.
* `[README.md](upsource:///README.md)`用于链接到存储库中的常规文件.
* `[`\`AnAction \``](upsource:///platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnAction.java)`用于指向接口和类的源代码文件的链接.
  请注意链接中类名周围使用的\`\`字符.

常规链接具有以下格式之一:
* `[外部网站](http://example.org)`链接到外部网站.
* 链接到SDK文档中的页面:
  * `[当前目录中的SDK doc页面](Page2.md)`链接到与当前页面相同的目录中的SDK doc页面.  
    请注意,扩展名为`.md`,_NOT_` .html`.
  * `[另一个文件夹中的SDK页面](/Folder2/Page2.md)`链接到另一个文件夹中的页面.
    请注意,URL正在从项目根目录导航 - 即使站点位于子文件夹中也是如此(例如,此链接适用于`http://localhost:4000/devguide/Folder2/Page2.html`).
    相对链接也有效(`../Folder2/Page2.md`).
* 链接到SDK文档中页面上的特定_sections_.
  锚名称全部为小写,空格用`-`替换,例如`## Page setup`变为`#page-setup`.
  * `[链接到当前页面上的部分](#another-section)`链接到当前页面上的标题.
  * `[链接到另一页上的部分](Page2.md#another-section)`链接到另一页上的标题.


### 备注和标注
可以使用blockquote语法指定注释和标注.
转换器查看下面的第一个单词以查看它是否为粗体.
如果是这样,它将应用标注样式.
以下示例将显示为标注,样式为“注释”:

    > **注意**这是一张纸条


> **注意**这是一张纸条


标注可用的样式是:
* TODO  - 通常不在SDK文档中使用.
  嵌入`<--! //TODO: explanation... -->` 相反,这在已发布的文档中不可见.
* 提示 - 使读者更有效率的信息.
* 注 - 对读者理解的重要信息.
  此标注保留用于关键点和概念.
* 警告 - 对用户至关重要的信息.
  使用此标注可获取可防止出现故障或错误的信息.


### 图片
可以通过将文件直接添加到`intellij-sdk-docs`存储库来包含图像.
每个主题目录通常都有一个`img`子目录.
使映像保持在存储库目录结构中的相应文档文件附近.

本文档中的图像通常是屏幕截图.
首选的图像格式是144 DPI分辨率的PNG.
分辨率为72 DPI是可以接受的,但在高分辨率显示器上可能看起来很模糊.


减小映像文件的大小以防止存储库膨胀并影响文档站点的性能非常重要.
在文档页面上将图像大小调整为几乎所需的宽度.
减小图像的尺寸是减少图像文件大小的最有效方法.
还可以使用[PNG优化器](https://plugins.jetbrains.com/plugin/7942-png-optimizer)等工具优化图像文件.

通过向图像添加Markdown链接将图像嵌入到文档中,如下所示:

    ![Alt text](path-to-img.png)

如果需要调整图像的宽度,请使用Kramdown标记:

    ![Alt text](path-to-img.png){:width =“42px”}


## _SUMMARY网站目录
站点的目录显示在站点左侧的树视图中,它是从`_SUMMARY.md`文件生成的.
它是一个简单的Markdown列表,列表中的每个项目都是指向另一个Markdown页面的链接,可以是根文件夹,也可以是子文件夹.
该列表可以具有嵌套项,这些项在内容列表中显示为子项.

> ***警告*** SDK存储库(`intellij-sdk-docs`)中的每个Markdown(`* .md`)文档必须在`_SUMMARY.md`中有一个条目.

```md
# Summary

* [Introduction](README.md)
* [About This Guide](Intro/About.md)
    * [Key Topics](Intro/KeyTopics.md)
```

通过将列表分成几个列表,每个新列表以2级标题(`##`)开头,可以将内容拆分为“部分”.

```md
# Summary

* [Introduction](README.md)
* [About This Guide](Intro/About.md)
    * [Key Topics](Intro/KeyTopics.md)

## Part I - Extending the Platform
* [Getting Started](Docs/GettingStarted.md)
* ...
```

如果某个节点没有链接但只是纯文本,它仍会显示在目录中,但会显示为灰色且无法点击.
它充当文档项的占位符.
占位符对于跟踪应该记录的内容很有用,但是还没有,并且可以向读者显示该主题存在,但尚未记录(拉请求总是受欢迎!).


