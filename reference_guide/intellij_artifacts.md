---
title: IntelliJ Platform Artifacts Repositories
---

JetBrains维护公共存储库,用于托管与IntelliJ平台相关的工件,例如二进制文件和源代码.
这些

存储库使插件开发人员更容易访问工件.


IntelliJ平台工件存储库是:

* [发布存储库](https://www.jetbrains.com/intellij-repository/releases/)了解发行版和EAP版本.

* [快照存储库](https://www.jetbrains.com/intellij-repository/snapshots/)用于EAP候选人和每个分支的最新EAP.


Releases和Snapshots存储库都有两种类型的内容:

*基于IntelliJ平台的IDE的跨平台ZIP分发的二进制和源代码工件,例如IntelliJ IDEA,CLion,Rider和MPS.

这些工件不是直接从插件项目的`build.gradle`文件中访问的.

`gradle-intellij-plugin`将根据插件项目的需要访问它们.

* IntelliJ平台中各个模块的工件.

这些可以下载,或直接从`build.gradle`文件访问,如下所述.
  

IntelliJ平台第三方依赖项的工件托管在[Bintray存储库](https://jetbrains.bintray.com/intellij-third-party-dependencies).

当使用IntelliJ平台工件库中的各个模块时,应将此存储库的链接添加到`pom.xml` /`build.gradle`文件中.


##使用IntelliJ平台模块工件

通过向项目的“build.gradle”文件添加信息来利用IntelliJ平台模块工件.

有关[Gradle支持](https://www.jetbrains.com/help/idea/gradle.html)的更多信息,请参见IntelliJ IDEA帮助文档.


要设置模块的依赖关系,需要两种类型的信息:

1.指定工件的相应存储库URL.

2.指定工件的Maven坐标.
 

###指定存储库URL

需要将所需工件的URL添加到Maven或Gradle脚本中:

*对于发行版或EAP版,请使用“https://www.jetbrains.com/intellij-repository/releases”

*对于EAP候选快照,请使用“https://www.jetbrains.com/intellij-repository/snapshots”

*对于IntelliJ平台中各个模块的依赖关系,也可以使用`https://jetbrains.bintray.com/intellij-third-party-dependencies`


###指定工件

使用Maven坐标描述所需的IntelliJ平台模块工件.

例如,要指定`jps-model-serialization`模块:
  
* _groupId_ =`com.jetbrains.intellij.platform`
  
* _artifactId_ =`jps-model-serialization`
  
* _classifier_ =`“”`
  
* _packaging_ =`jar`


对于每个工件[在存储库URL](#signate-the-repository-url),有多个版本可用.
可以通过以下几种方式之一指定版本:

*分支构建指定为_BRANCH.BUILD [.FIX] _.
例如,一个分支构建,例如`141.233`,或一个分支构建,例如`139.555.1`

*版本号指定为_MAJOR [.MINOR] [.FIX] _.
例如`14`,或`14.1`,或`14.1.1`

*将生成下一个EAP /发布版本的分支的快照指定为_BRANCH-EAP-SNAPSHOT_.
例如`141-EAP-SNAPSHOT`


## IntelliJ平台的单个模块的Gradle示例

本节介绍使用Gradle脚本将IntelliJ平台模块和存储库合并到`build.gradle`文件中的示例.

该示例说明了声明`jps-model-serialization`模块工件的工件URL,Maven坐标和版本.

该示例分为两部分:存储库和依赖关系部分.


### Repositories Section  
This code snippet selects the release repository with the first URL, and repository of IntelliJ Platform dependencies with the second URL.
The second URL is needed because this example selects individual modules. 
```groovy
repositories {
	maven { url "https://www.jetbrains.com/intellij-repository/releases" }
	maven { url "https://jetbrains.bintray.com/intellij-third-party-dependencies" }
}
```

### Dependencies Section  
This code snippet specifies the desired module artifacts.
```groovy
dependencies {
	compile "com.jetbrains.intellij.platform:jps-model-serialization:182.2949.4"
	compile "com.jetbrains.intellij.platform:jps-model-impl:182.2949.4"
}
```
Note:
 * The artifact version (`182.2949.4`) must match in both statements.
 * In this example `jps-model-serialization` declares the APIs and `jps-model-impl` provides the implementation, so both
   are required dependencies.


