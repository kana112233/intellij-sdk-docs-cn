---
title: UI Themes - Editor Schemes and Background Images
---

UI主题还可以提供自定义颜色和字体设置,以及在IntelliJ IDEA应用程序窗口中显示的自定义图像.
 

##添加自定义编辑器方案

基于IntelliJ平台的IDE(例如Intellij IDEA)的用户可以设置首选项以配置编辑器中使用的颜色和字体.

这些自定义颜色和字体设置称为_Editor Color Schemes_.


###使用设置/首选项创建自定义编辑器方案

可以使用IDE _Settings/Preferences_对话框指定和导出自定义编辑器颜色方案.

请注意编辑器[颜色和字体](https://www.jetbrains.com/help/idea/configuring-colors-and-fonts.html)和[颜色版本控制文件状态](https://www.jetbrains.com/help/idea/file-status-highlights.html)在_Settings/Preferences_的不同部分进行了自定义.


使用以下过程为UI主题自定义编辑器颜色方案:

*使用IDE首选项创建所需的自定义编辑器颜色方案.

*将自定义编辑器颜色方案导出到所需的文件名.
  
在此示例中,文件将导出为“Lightning.icls”

*导出后,将文件扩展名从`* .icls`更改为`* .xml`.
  
在这个例子中,结果是`Lightning.xml`.

*请参阅[自定义编辑器滚动条颜色](#customizeizing-editor-scroll-bar-colors)以更改编辑器滚动条的颜色.


###在自定义UI主题中包含编辑器颜色方案

下一步是将颜色方案添加到UI Theme插件项目:

*使用导出的自定义编辑器颜色方案替换`resources`文件夹中的默认生成的自定义编辑器颜色方案XML文件(在此示例中为`theme_basics.xml`).
  
在这种情况下,操作是_replace_` theme_basics.xml`和`Lightning.xml`.

*在UI主题文件(在此示例中为`theme_basics.theme.json`,)中,使用新的(`Lightning.xml`)文件名替换生成的编辑器方案文件(`theme_basics.xml`)的名称.
  
`key`始终是“editorScheme”.
  
`value`是编辑器配色方案文件的名称.


The example below adds an editor scheme named "Lightning" to the _Theme Basics_ custom UI Theme:
```json
{
  "name": "Theme Basics",
  "dark": false,
  "author": "IntelliJ Platform SDK",
  "editorScheme": "/Lightning.xml",
  "ui": {
  }
}
```

### Editor Color Scheme XML Files
When an editor color scheme is exported as a file, the color options appear as `name`-`value` attributes of `option` elements.
The `name` is the aspect of the editor to be changed, and the `value` is the new color in six-digit RGB or eight-digit RGBA hexadecimal notation.
For example, the snippet below sets the color of the line numbers displayed in the editor:
```xml
<colors>
  <option name="LINE_NUMBERS_COLOR" value="999999" />
</colors>
```
For additional examples of `name` and `value` attributes, review the editor color scheme XML file for the [High Contrast editor scheme](upsource:///platform/platform-resources/src/themes/highContrastScheme.xml).

### Customizing Version Control File Status Colors
As [described above](#creating-a-custom-editor-scheme-using-settingspreferences), colors corresponding to the VCS status of files can be customized and exported via the [IntelliJ IDEA Preferences/Settings](https://www.jetbrains.com/help/idea/file-status-highlights.html).
No other procedure is necessary to customize these colors.
In the exported color scheme file the `name` is the VCS file status, and the `value` is the new color corresponding to that status.
For example, customized VCS colors for a subset of file statuses will appear in the editor scheme file as:
```xml
<colors>
  <option name="FILESTATUS_ADDED" value="62cc47" />
  <option name="FILESTATUS_COPIED" value="62cc47" />
  <option name="FILESTATUS_DELETED" value="ed864a" />
</colors>

```
For additional examples of `FILESTATUS` color `name` attributes, see the editor color scheme XML file for the [High Contrast editor scheme](upsource:///platform/platform-resources/src/themes/highContrastScheme.xml).

###自定义编辑器滚动条颜色

编辑器滚动条颜色应与之协调,并与编辑器颜色方案一起切换.

请注意,自定义UI主题(`* .theme.json`)文件也包含`ScrollBar.*`名称属性,但这些属性适用于编辑器上下文之外的滚动条.


> **注意**编辑器滚动条颜色是唯一无法通过IDE首选项自定义和导出的编辑器方案设置.


自定义编辑器滚动条颜色需要手动更改编辑器颜色方案XML文件.

此时,没有用于更改自定义颜色编辑器方案XML文件的代码完成功能,因此`name`属性如下所述.


####编辑器滚动条属性名称格式

滚动条`name`属性的典型格式是`ScrollBar.usage`,其中`usage`描述了颜色的应用位置.

在某些情况下,`usage`本身可以是复合,例如`ScrollBar.Mac.Transparent.thumbColor`.

在这些复合情况下,化合物“用法”的最后部分仍然描述了颜色的应用位置.


Note that the following example snippet uses an eight-digit hexadecimal color `value` to give `ScrollBar.Mac.thumbColor` transparency:
```xml
<color>
<option name="ScrollBar.Mac.trackColor" value="000000"/>
<option name="ScrollBar.Mac.thumbColor" value="FFFFFFBE"/>
</color>
```

####编辑器滚动条属性名称

滚动条“名称”属性列表位于[高对比度编辑器方案](upsource:///platform/platform-resources/src/themes/highContrastScheme.xml)文件中.

目前无法在IDE UI中的任何位置访问这些名称属性,因此必须将它们手动添加到编辑器颜色方案XML文件中.


以下列表解释了`name`属性的`usage`格式,即应用自定义滚动条颜色的位置:

*`* .trackColor`  - 滚动条拇指在此区域移动.
  
此时垂直滚动条轨道颜色和透明度无法自定义.

*`* .thumbColor`  - 对应于可见内容大小的可移动矩形.

*`* .thumbBorderColor`  - 拇指边框.

*`* .hoverTrackColor`  - 与上面的'用法'相同,但用于悬停.
  
此时垂直滚动条悬停轨道颜色和透明度无法自定义.

*`* .hoverThumbColor`  - 与上面相同的'用法',但用于悬停.

*`* .hoverThumbBorderColor`  - 与上面的'用法'相同,但用于悬停.


`name`属性模式列举如下.


**平台无关名称属性**


水平滚动条背景颜色由`ScrollBar.background`设置.

仅当水平滚动条的“* .trackColor”具有透明度时,此背景颜色才可见.


此时垂直滚动条背景颜色无法自定义.


** Windows/Linux名称属性**


Windows和Linux滚动条的`name`属性具有模式`ScrollBar.Transparent.*`,其中通配符部分对应于上面的'usage`定义.


** macOS名称属性**


垂直滚动条的`name`属性模式是`ScrollBar.Mac.*`.


水平滚动条的`name`属性模式取决于macOS首选项_Show scroll bars_ setting:
  
*选择_Always_设置时的`ScrollBar.Mac.*`.
  
*`ScrollBar.Mac.Transparent.*`当_When scrolling_设置被选中时.


这些模式的通配符部分对应于上面的“用法”定义.


##添加自定义背景图像

IDE支持在应用程序窗口中将图像设置为背景.

用户可以在[首选项](https://www.jetbrains.com/help/idea/setting-background-image.html)中手动执行此操作.


UI主题支持将背景图像指定为主题描述文件的“背景”:{}`部分中的键值对:

*`image`键使用图像的文件名作为值.

背景图像放在UI Theme插件项目的resources文件夹中.

*`transparency`键使用1-100的`value`.

“值”100是不透明的.

*`fill`键使用`scale`值,意味着在窗口调整大小时展开图像以填充空间.

*`anchor`键使用值“center”,意思是在窗口中心定位图像的中心.



The following example adds an image of the Austrian countryside to the _Theme Basics_
Theme description file:
```json
{
  "name": "Theme Basics",
  "dark": false,
  "author": "IntelliJ Platform SDK",
  "ui": {
  },
  "background": {
    "image": "/austria.png",
    "transparency": 10,
    "fill": "scale",
    "anchor": "center"
  }
}
```


