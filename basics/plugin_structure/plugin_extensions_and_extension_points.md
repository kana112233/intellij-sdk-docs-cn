---
title: Plugin Extensions and Extension Points
---

 *IntelliJ Platform* 提供了_extensions_和_extension points_的概念,允许插件与其他插件或IDE本身进行交互.


## 扩展点


如果您希望插件允许其他插件扩展其功能,则在插件中,您必须声明一个或多个_extension points_.
每个扩展点定义允许访问此点的类或接口.


## 扩展


如果您希望插件扩展其他插件或 *IntelliJ Platform* 的功能,则必须声明一个或多个_extensions_.


## 如何声明扩展和扩展点


您可以分别在插件配置文件`plugin.xml`中声明扩展和扩展点,分别在`<extensions>`和`<extensionPoints>`部分中.


**申报延期点**


在`<extensionPoints>`部分中,插入一个子元素`<extensionPoint>`,它定义了扩展点名称和bean类的名称,或者允许在`name`中扩展插件功能的接口,`beanClass 
`和`interface`属性.


要澄清此过程,请考虑plugin.xml文件的以下示例部分:


```xml
<extensionPoints>
  <extensionPoint name="MyExtensionPoint1" beanClass="MyPlugin.MyBeanClass1">
  <extensionPoint name="MyExtensionPoint2" interface="MyPlugin.MyInterface">
</extensionPoints>
```

* `interface`属性设置一个接口,该插件有助于扩展点必须实现.

* `beanClass`属性设置一个bean类,它指定一个或多个用[@Attribute]注释的属性(upsource:///platform/util/src/com/intellij/util/xmlb/annotations/Attribute.java)注释.


有助于扩展点的插件将从`plugin.xml`文件中读取这些属性.


为了澄清这一点,请考虑以下`plugin.xml`文件中使用的示例`MyBeanClass1` bean类:


```java
public class MyBeanClass1 extends AbstractExtensionPointBean {
  @Attribute("key")
  public String key;

  @Attribute("implementationClass")
  public String implementationClass;

  public String getKey() {
    return key;
  }

  public String getClass() {
    return implementationClass;
  }
}
```

要声明一个用于访问`MyExtensionPoint1`扩展点的扩展,你的`plugin.xml`文件必须包含`<MyExtensionPoint1>`标签,其中`key`和`implementationClass`属性设置为适当的值(见下面的示例).


**申报延期**


> **提示**所有这些步骤均可使用自动完成功能.


1.对于`<extensions>`元素,将`defaultExtensionNs`属性设置为以下值之一:
    
*`com.intellij`,如果你的插件扩展了IntelliJ平台的核心功能.
    
*`{插件的ID},如果你的插件扩展了另一个插件的功能.

2.在`<extensions>`元素中添加一个新的子元素.
子元素名称必须与您希望扩展程序访问的扩展点的名称相匹配.

3.根据扩展点的类型,执行以下操作之一:
    
*如果使用`interface`属性声明扩展点,则对于新添加的子元素,将`implementation`属性设置为实现指定接口的类的名称.
    
*如果使用`beanClass`属性声明扩展点,则对于新添加的子元素,设置所有使用[@Attribute]注释的属性(upsource:///xml/dom-openapi/src/com/intellij/util/指定bean类中的xml/Attribute.java)注释.


为了澄清这个过程,请考虑`plugin.xml`文件的以下示例部分,它定义了两个扩展,旨在访问 *IntelliJ Platform* 中的`appStarter`和`applicationConfigurable`扩展点,以及一个访问`MyExtensionPoint1的扩展名.
测试插件中的扩展点:


```xml
<!-- Declare extensions to access extension points in the IntelliJ Platform.
     These extension points have been declared using the "interface" attribute.
 -->
  <extensions defaultExtensionNs="com.intellij">
    <appStarter implementation="MyTestPackage.MyTestExtension1" />
    <applicationConfigurable implementation="MyTestPackage.MyTestExtension2" />
  </extensions>

<!-- Declare extensions to access extension points in a custom plugin
     The MyExtensionPoint1 extension point has been declared using *beanClass* attribute.
-->
  <extensions defaultExtensionNs="MyPluginID">
     <MyExtensionPoint1 key="keyValue" implementationClass="MyTestPackage.MyClassImpl"></MyExtensionPoint1>
  </extensions>
```
### Extension default properties
The following properties are available always:

- `id` - unique ID
- `order` - allows to order all defined extensions using `first`, `last` or `before|after [id]` respectively
- `os` - allows to restrict extension to given OS, e.g., `os="windows"` registers the extension on Windows only 

##如何获得扩展点列表？


要获取 *IntelliJ Platform* 核心中可用的扩展点列表,请参阅以下XML配置文件的`<extensionPoints>`部分:


* [`LangExtensionPoints.xml`](upsource:///platform/platform-resources/src/META-INF/LangExtensionPoints.xml)

* [`PlatformExtensionPoints.xml`](upsource:///platform/platform-resources/src/META-INF/PlatformExtensionPoints.xml)

* [`VcsExtensionPoints.xml`](upsource:///platform/platform-resources/src/META-INF/VcsExtensionPoints.xml)


