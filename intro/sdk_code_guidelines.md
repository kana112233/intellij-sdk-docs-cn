---
title: Guidelines for Creating IntelliJ Platform SDK Code Samples
---

本文档描述了用于编写开源IntelliJ Platform SDK代码示例的编码指南.

在开始之前,请仔细阅读本页以及[行为准则](/CODE_OF_CONDUCT.md)和[许可证](https://github.com/JetBrains/intellij-sdk-docs/blob/master/LICENSE.txt)文件.

有关为IntelliJ平台本身做出贡献的信息,请访问[贡献IntelliJ平台](/basics/platform_contributions.md).


## 目标

在编写SDK代码示例时,请记住以下注意事项:

* SDK示例的目的是演示在IntelliJ平台的子系统和组件之上构建的实现模式.

* SDK代码示例是教学代码,旨在教授.
  
* 教学代码在某些关键方面与生产代码不同:
    
* 为了简单和简洁,牺牲一些稳健性.
      
使用错误检查有必要对实施陷阱提出一个观点.
    
* 尽可能简化实现,但使用IntelliJ平台,Java语言和库的全部功能.
    
* 为接口,类,字段,方法和扩展点使用有意义的名称.
    
* 编写指导JavaDoc注释.
  
* 代码示例取代了大量文档.

* 针对两个级别的SDK示例:
  
* 通过演示IntelliJ平台的有限区域,_basic_示例专注于特定主题.
    
它应该显示所有组件和架构,但最终会完成一些基本的东西.
    
例如,通过仅存储`String`来演示持久性.
  
* _advanced_示例演示了IntelliJ平台的不同部分如何集成和协同工作.
    
例如,将检查或意图与非平凡的PsiTree操作相结合.
 

最终,目标是为开发人员提供在基于IntelliJ平台的插件中实现功能的路线图.

每个路线图应包含:

* 指向实现该功能所需的IntelliJ Platform API的SDK文档.

* 指向相关的_basic_ SDK示例插件的指针.

* 指向相关的_advanced_ SDK示例插件的指针.


## SDK插件的命名约定

对于_basic_ samples,命名约定集中在正在演示的IntelliJ Platform API上.

例如,`foo_basics`其中`foo`对应于IntelliJ平台功能,框架或组件.

一些命名示例包括`facet_basics`和`editor_basics`.

每个IntelliJ Platform API区域只有一个_basic_示例.


对于_advanced_代码示例,名称应该反映插件而不是IntelliJ Platform API提供的复杂功能.

高级样本将与示例中演示的IntelliJ Platform API交叉引用.


无论基本版还是高级版,SDK插件名称在Gradle插件向导中也称为“工件ID”.


## Plugin Copyright Statements
Use the standard intellij-community copyright notice in all sample plugins authored by JetBrains:
```text  
Copyright 2000-$today.year JetBrains s.r.o. Use of this source code is governed by the Apache 2.0 license that can be found in the LICENSE file."  
```
The copyright statement must appear in every source file with the `$today.year` [Velocity](https://www.jetbrains.com/help/idea/copyright-profiles.html) template resolved.

## 插件ID约定

插件ID(`plugin.xml`中的`<id>`)总是以`org.intellij.sdk`开头.

插件ID在Gradle插件向导中称为“组ID”.


对于_basic_代码示例,不必在插件ID中包含“basic”.

像`facet_basics`这样的插件有ID`org.intellij.sdk.facet`.


## 插件包名称

插件中的包应以插件ID开头.

如果插件中只有一个包,则包名称与插件ID相同.


## Plugin Directory Structure
SDK sample code should have a standard directory footprint.
Standardized structure not only makes the samples easier to navigate and understand, but it builds on the default Gradle plugin project structure.
The following is the directory structure for a `foo_basics` plugin.
```text
code_samples/
  foo_basics/
    gradle/
    src/
      main/
        java/
          icons/
            FooBasicsIcons.java
        resources/
          icons/
            sdk.svg           # The standard SDK icon for menus, etc.
          META-INF/
            plugin.xml        # The plugin configuration file
            pluginIcon.svg    # The standard SDK plugin icon
      test/                   # Omit if there are no tests
        java/
        resources/
    build.gradle
    gradlew
    gradle.bat
    settings.gradle
```

## build.gradle约定

应该[使用Gradle](/tutorials/build_system.md)开发新的SDK代码示例.

在撰写本文时,在SDK代码示例中使用Gradle仍然在很大程度上依赖于`plugin.xml`来指定插件配置.

在稍后的第二阶段,SDK代码示例将转换为更多地依赖Gradle配置.


`build.gradle`文件的默认内容由[Gradle项目向导](/tutorials/build_system/prerequisites.md#create-a-plugin-project-from-scratch)生成.

SDK代码示例的“build.gradle”文件的一致结构对于清晰度很重要,并且基于项目向导生成的默认值.

SDK代码示例`build.gradle`文件中的注释应仅引起对Gradle配置中对插件唯一的部分的注意.


For SDK code samples a few alterations are needed to the default build.gradle file produced by the plugin wizard:
* Add the following statements to the [Setup DSL](https://github.com/JetBrains/gradle-intellij-plugin/blob/master/README.md#setup-dsl) (`intellij{}`) section:
  ```groovy
      // Prevents patching <idea-version> attributes in plugin.xml
      updateSinceUntilBuild = false 
      // Define a shared sandbox directory for running code sample plugins within an IDE.
      sandboxDirectory = file("${project.projectDir}/../_idea-sandbox")
  ``` 
* Remove the [Patching DSL](https://github.com/JetBrains/gradle-intellij-plugin/blob/master/README.md#patching-dsl) (`patchPluginXml{}`) section.
  It is not needed in SDK samples.
* Add the `javadoc` task to build documentation for the code sample:
  ```groovy
    // Force javadoc rebuild before jar is built
    jar.dependsOn javadoc
  ```

## plugin.xml约定

SDK代码示例的[`plugin.xml`](/basics/plugin_structure/plugin_configuration_file.md)配置文件的一致结构非常重要,因为我们希望引起对插件文件的独特部分的注意.

不一致的元素顺序在视觉上是嘈杂的.

大量关于独特元素和配置的评论,并谨慎地评论其余部分和配置.


SDK代码示例`plugin.xml`文件中的元素序列是:

* `<id>`使用完全限定的[插件ID](#plugin-id-conventions).

* `<name>`名称值不必与[插件名称]匹配(#naming-conventions-for-sdk-plugins).
  
它可能反映了插件的功能.
  
名称必须以“SDK:”开头.

* `<version>`MAJOR.MINOR.FIX格式的代码示例版本.
  
* MAJOR对应于功能的重大升级.
  
* MINOR对应于次要重构和功能上的小改进.
  
* FIX对应于在没有重大重构的情况下修复问题的更改.

* `<idea-version/>`设置属性:
  
* `since-build`属性是IntelliJ平台最早的兼容版本号.
  
* `until-build`属性为空字符串`“”`.
    
每个主要版本都会审核SDK代码示例,以确保与最新的IntelliJ平台兼容.

* `<description>`是对所演示内容以及用户如何访问该功能的简洁说明.

* `<change-notes>`是按版本号排列的有序列表,其中简要描述了每个版本的更改.

* `<vendor>`将值设置为`IntelliJ Platform SDK`.
  
设置属性:
  
* `email`省略此属性.
  
* `url`到JetBrains插件库`“https://plugins.jetbrains.com”

* [插件配置元素]的其余部分(/basics/plugin_structure/plugin_configuration_file.md)只应在特定插件需要时才会出现.


## 测试

IntelliJ Platform SDK代码示例应该根据`since-build`版本构建和测试.


代码示例应该干净地构建,没有警告或错误,新的代码示例应该通过所有默认的IntelliJ IDEA代码检查.


Testers should complete the following checklist. 
Here the term "IDE" means the IntelliJ Platform-based IDE in which the plugin is designed to run:
* The plugin should load in the IDE.
* The correct information about the plugin should display in the **Preferences \| Plugins** panel.
* If applicable, the plugin UI such as tool windows, menu additions, etc. should display correctly.
* The functionality of the plugin should be tested with a sample file.
* If applicable, the plugin should pass unit tests.


