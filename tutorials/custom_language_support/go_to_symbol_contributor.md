---
title: 13. Go To Symbol Contributor
---

*转到符号贡献者可帮助用户按名称导航到任何PSI元素。*


### 13.1。
为生成的PSI元素定义辅助方法


要在* Go To Symbol *弹出窗口，* Structure *工具窗口或其他组件中指定PSI元素的外观，它应该实现* getPresentation *方法。


这意味着我们需要在我们的实用程序* com.simpleplugin.psi.impl.SimplePsiImplUtil *中定义此方法，并重新生成解析器和PSI类。


```java
public static ItemPresentation getPresentation(final SimpleProperty element) {
    return new ItemPresentation() {
        @Nullable
        @Override
        public String getPresentableText() {
            return element.getKey();
        }

        @Nullable
        @Override
        public String getLocationString() {
            return element.getContainingFile().getName();
        }

        @Nullable
        @Override
        public Icon getIcon(boolean unused) {
            return SimpleIcons.FILE;
        }
    };
}
```

### 13.2。
更新语法并重新生成解析器


```java
property ::= (KEY? SEPARATOR VALUE?) | KEY {mixin="com.simpleplugin.psi.impl.SimpleNamedElementImpl"
  implements="com.simpleplugin.psi.SimpleNamedElement" methods=[getKey getValue getName setName getNameIdentifier getPresentation]}
```

通过右键单击`Simple.bnf`文件并选择_Generate Parser Code_来重新生成解析器。


### 13.3。
定义转到符号贡献者


```java
{% include /code_samples/simple_language_plugin/src/com/simpleplugin/SimpleChooseByNameContributor.java %}
```

### 13.4。
注册转到符号贡献者


```xml
<gotoSymbolContributor implementation="com.simpleplugin.SimpleChooseByNameContributor"/>
```

### 13.5。
运行该项目


Now we can navigate to a property definition by name pattern via **Navigate \| Symbol** action.

![转到符号](img/go_to_symbol.png)


