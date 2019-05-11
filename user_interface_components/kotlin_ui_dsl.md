---
title: Kotlin UI DSL
---

> **注意**本文档涵盖IntelliJ IDEA 2019.2中的Kotlin UI DSL。
本文档中描述的许多功能不适用于针对早期IntelliJ IDEA版本的插件。


## 布局结构


使用`panel`创建UI:


```kotlin
panel {
  row {
    // child components
  }
}
```

行从上到下垂直创建，顺序与调用`row`的代码行相同。

在一行内部，您可以按相同的顺序向工厂方法添加从左到右的组件，或者在每行中显示`()`。

每个组件都有效地放置在自己的网格单元中。


可以将行的标签指定为`row`方法的参数:


```kotlin
row("Parameters") { ... }
```

行可以嵌套。
嵌套行块中的组件被视为从属于包含行，并相应地缩进。


```kotlin
row {
  checkBox(...)
  row {
    textField(...) // indented relatively to the checkbox above
  }
}
```

要将多个组件放在同一个网格单元格中，请将它们包装在`cell`方法中:


```kotlin
row {
  // These two components will occupy two columns in the grid
  label(...)
  textField(...)

  // These two components will be placed in the same grid cell
  cell {
    label(...)
    textField(...)
  }
}
```

要将组件放在网格行的右侧，请使用“right”方法:


```kotlin
row {
  rememberCheckBox()
  right {
    link("Forgot password")
  }
}
```

## 添加组件


There are two ways to add child components. The recommended way is to use factory methods `label`, `button`, `radioButton`, `hint`, `link`, etc. It allows you to create consistent UI and reuse common patterns.
  ```kotlin
  note("""Do not have an account? <a href="https://account.jetbrains.com/login">Sign Up</a>""", span, wrap)
  ```

These method also support **property bindings**, allowing you to automatically load the value displayed in the component from a property and to store it back. The easiest way to do that is to pass a reference to a Kotlin bound property:
```kotlin
checkBox("Show tabs in single row", uiSettings::scrollTabLayoutInEditor)
```

或者，许多工厂方法支持在属性映射更复杂的情况下指定getter/setter对:


```kotlin
  checkBox(
    "Show file extensions in editor tabs",
    { !uiSettings.hideKnownExtensionInTabs },
    { uiSettings.hideKnownExtensionInTabs = !it }
```

If you want to add a component for which there are no factory methods, you can simply invoke an instance of your component, using the `()` overloaded operator:
  ```kotlin
  val userField = JTextField(credentials?.userName)
  panel() {
    row { userField(grow, wrap) }
  }
  // use userField variable somehow
  ```

## 支持的组件


### 标签


使用`label`方法:


```kotlin
label("Sample text")
```

### 复选框


见上面的例子。


### 单选按钮


单选按钮组使用`buttonGroup`块创建。
有两种方法可以使用它。
如果所选单选按钮对应于单个特定值

property，将属性绑定传递给`buttonGroup`方法，将特定值传递给`radioButton`函数:


```kotlin
buttonGroup(mySettings::providerType) {
  row { radioButton("In native Keychain", ProviderType.KEYCHAIN) }
  row { radioButton("In KeePass", ProviderType.KEEPASS) }
}
```

如果所选单选按钮由多个布尔属性控制，则使用不带绑定的`buttonGroup`，并为除一个单选按钮之外的所有按钮指定属性绑定:


```kotlin
buttonGroup {
  row { radioButton("The tab on the left") }
  row { radioButton("The tab on the right", uiSettings::activeRightEditorOnClose) }
  row { radioButton("Most recently opened tab", uiSettings::activeMruEditorOnClose) }
}
```

### 文本字段


将`textField`方法用于简单的文本字段:


```kotlin
row("Parameters:") {
    textField(settings::mergeParameters)
}
```

要输入数字，请使用`intTextField`:


```kotlin
intTextField(uiSettings::editorTabLimit, columns = 4, range = EDITOR_TABS_RANGE)
```

对于密码文本字段，没有可用的工厂函数，因此您需要使用`()`:


```kotlin
val passwordField = JPasswordField()
val panel = panel {
  // ...
  row { passwordField() }
}
```

要指定文本字段的大小，请传递`columns`参数，如上面的`intTextField`示例所示，或指定`growPolicy`参数:


```kotlin
val userField = JTextField(credentials?.userName)
val panel = panel {
    row("Username:") { userField(growPolicy = GrowPolicy.SHORT_TEXT) }
}
```

### 组合框


将`comboBox`方法与bound属性或getter/setter对一起使用:


```kotlin
comboBox(DefaultComboBoxModel<Int>(tabPlacements), uiSettings::editorTabPlacement)

comboBox<PgpKey>(
  pgpListModel,
  { getSelectedPgpKey() ?: pgpListModel.items.firstOrNull() },
  { mySettings.state.pgpKeyId = if (usePgpKey.isSelected) it?.keyId else null })
```

### 纺纱厂


使用`spinner`方法:


```kotlin
spinner(retypeOptions::retypeDelay, minValue = 0, maxValue = 5000, step = 50)
```

### 链接标签


使用`link`方法:


```kotlin
link("Forgot password?") {
  BrowserUtil.browse("https://account.jetbrains.com/forgot-password")
}
```

### 分隔符


使用`titledRow`方法并将分隔符下的控件放入嵌套块中:


```kotlin
titledRow("Appearance") {
  row { checkBox(...) }
}
```

### 解释性文字


使用`comment`参数:


```kotlin
checkBox(message("checkbox.smart.tab.reuse"),
       uiSettings::reuseNotModifiedTabs,
       comment = message("checkbox.smart.tab.reuse.inline.help"))
```

## 使用属性绑定集成面板


`panel`方法返回的面板是`DialogPanel`的实例。
该基类支持标准的`apply`，`reset`和`isModified`方法。


如果您使用此面板作为`DialogWrapper`的主面板，则在使用OK操作关闭对话框时将自动调用`apply`方法。
在这种情况下，其他方法未使用。


如果您使用此面板实现`Configurable`，请使用`BoundConfigurable`作为基类。
在这种情况下，`Configurable`方法将自动委托给面板。


## 启用和禁用控件


使用`enableIf`方法将控件的启用状态绑定到在其他控件中输入的值。
该方法的参数是**谓词**。


```kotlin
checkBox("Show tabs in single row", uiSettings::scrollTabLayoutInEditor)
  .enableIf(myEditorTabPlacement.selectedValueIs(SwingConstants.TOP))
```

可用的谓词是:
  
*`selected`检查复选框或单选按钮的选定状态
  
*`selectedValueIs`和`selectedValueMatches`来检查组合框中的选定项目。
  

谓词可以与`和`以及`或'中缀函数结合使用:


```kotlin
checkBox("Hide tabs if there is no space", uiSettings::hideTabsIfNeed)
  .enableIf(myEditorTabPlacement.selectedValueMatches { it != UISettings.TABS_NONE } and
              myScrollTabLayoutInEditorCheckBox.selected)
```

## 示例


```kotlin
val panel = panel {
  noteRow("Login to get notified when the submitted\nexceptions are fixed.")
  row("Username:") { userField() }
  row("Password:") { passwordField() }
  row {
    rememberCheckBox()
    right {
      link("Forgot password?") { /* custom action */ }
    }
  }
  noteRow("""Do not have an account? <a href="https://account.jetbrains.com/login">Sign Up</a>""")
}
```

# #  常问问题


### 一个单元格最小，第二个单元格最大


为第二个单元格中的某个组件设置`CCFlags.growX`和`CCFlags.pushX`。


