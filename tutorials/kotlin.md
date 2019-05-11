---
title: Kotlin for Plugin Developers
---

## 1.为什么选择Kotlin?


使用Kotlin为IntelliJ平台编写插件与在Java中编写插件非常相似.
现有的插件开发人员可以通过使用[J2K编译器]将样板Java类转换为他们的Kotlin等价物来开始(https://kotlinlang.org/docs/tutorials/mixing-java-kotlin-intellij.html#converting-an-existing-java-file-to-kotlin-with-j2k)与IntelliJ平台(版本143. +)捆绑在一起,开发人员可以轻松地将Kotlin类与现有的Java代码混合搭配.


除了[null safety](https://kotlinlang.org/docs/reference/null-safety.html)和[type-safe builders](https://kotlinlang.org/docs/reference/type-safe-builders.html),Kotlin语言为插件开发提供了许多方便的功能,使插件更易于阅读和维护.
与[Kotlin for Android](https://kotlinlang.org/docs/tutorials/kotlin-android.html)非常相似,IntelliJ平台广泛使用回调,这些回调很容易表达为[lambdas](https://kotlinlang.org/docs/reference/lambdas.html).


同样,使用[extensions](https://kotlinlang.org/docs/reference/extensions.html)可以轻松地在IntelliJ IDEA中自定义内部类的行为.
例如,通常的做法是[保护日志声明](https://www.slf4j.org/faq.html#logging_performance)以避免参数构建的成本,在使用日志时导致以下仪式:


```java
if(logger.isDebugEnabled()) {
  logger.debug("...");
}
```

通过声明以下扩展方法,我们可以在Kotlin中更简洁地实现相同的结果:


```kotlin
inline fun Logger.debug(lazyMessage: () -> String) {
  if (isDebugEnabled) {
    debug(lazyMessage())
  }
}
```

现在我们可以直接编写`logger.debug {“...”}来获得轻量级日志记录的所有好处,没有任何冗长.
通过练习,您将能够在IntelliJ平台中识别出许多可以通过Kotlin简化的习语.
要了解有关使用Kotlin构建IntelliJ平台插件的更多信息,本教程将帮助您入门.


## 2.添加Kotlin支持


针对IntelliJ平台版本143及更高版本的插件易于迁移:刚开始编写Kotlin.
必要的Kotlin插件和库已与IDE捆绑在一起,无需进一步配置.
对于142或更低版本,您需要安装和配置Kotlin运行时的依赖项(除了安装Kotlin插件本身,还提供代码帮助和工具支持).
有关详细说明,请参阅[Kotlin文档](https://kotlinlang.org/docs/tutorials/getting-started.html).


## 3. Kotlin Gradle插件


对于已经使用[Gradle Build System](build_system.md)的插件,或那些需要对Kotlin构建过程进行精确控制的插件,我们建议使用[kotlin-gradle-plugin](https://kotlinlang.org/docs/reference/using-gradle.html#configuring-dependencies). This [Gradle plugin](https://mvnrepository.com/artifact/org.jetbrains.kotlin/kotlin-gradle-plugin-core)极大地简化了以受控且可重现的方式构建Kotlin项目的过程.


您的`build.gradle`文件可能如下所示:


```groovy
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}

apply plugin: 'org.jetbrains.intellij'
apply plugin: 'kotlin'

intellij {
    version 'LATEST-TRUNK-SNAPSHOT'
    pluginName 'Example'
}

group 'com.example'
version '0.0.1'

repositories {
    mavenCentral()
}
```

请注意,你应该**不要用你的插件包含`kotlin-runtime`和`kotlin-stdlib` jars,因为Kotlin保证了后向和前向二进制兼容性.


### 3.1.
使用Kotlin编写Gradle脚本


从4.4开始,Gradle支持`build.gradle.kts`,它是Kotlin编写的`build.gradle`的替代品.


学习如何使用Kotlin脚本为IntelliJ插件编写构建脚本有很多很好的资源,比如[intellij-rust](https://github.com/intellij-rust/intellij-rust/blob/master/build.gradle.kts),[julia-intellij](https://github.com/ice1000/julia-intellij/blob/master/build.gradle.kts),[covscript-intellij](https://github.com/covscript/covscript-intellij/blob/master/build.gradle.kts)或[zig-intellij](https://github.com/ice1000/zig-intellij/blob/master/build.gradle.kts).


`build.gradle.kts`基本上看起来像:


```kotlin
group = "com.your.company.name"
version = "0.1-SNAPSHOT"

buildscript {
    repositories { mavenCentral() }
    dependencies { classpath(kotlin("gradle-plugin", "1.2.30")) }
}

plugins {
	id("org.jetbrains.intellij") version "0.4.5"
	kotlin("jvm") version "1.2.30"
}

intellij {
    updateSinceUntilBuild = false
    instrumentCode = true
    version = "2017.3"
}
```

## 4.Kotlin的用户界面


使用Kotlin创建用户界面的最佳方法是使用类型安全的DSL来构建表单而不是GUI设计器. 
IntelliJ平台中使用的DSL位于`com.intellij.ui.layout`包中. 
[文档](upsource:///platform/platform-impl/src/com/intellij/ui/layout/readme.md).


## 5.处理Kotlin代码


如果您需要编写处理Kotlin代码的插件,则需要在Kotlin插件上添加依赖项.
请参考

到[Plugin Dependencies](/basics/plugin_structure/plugin_dependencies.md)获取有关如何执行此操作的信息.


## 6.例子


在IntelliJ平台上构建了许多[开源Kotlin项目](https://github.com/search?l=Kotlin&q=+intellij&ref=searchresults&type=Repositories).
有关使用IntelliJ平台构建开发人员工具的Kotlin语言的最新示例和应用程序的现成资源,开发人员可以查看这些项目的灵感:


* [intellij-rust](https://github.com/intellij-rust/intellij-rust)

* [haskell-idea-plugin](https://github.com/atsky/haskell-idea-plugin)

* [intellij-markdown](https://github.com/valich/intellij-markdown)

* [IntelliJ-presentation-assistant](https://github.com/chashnikov/IntelliJ-presentation-assistant)

* [leanpub-plugin](https://github.com/hhariri/leanpub-plugin)


