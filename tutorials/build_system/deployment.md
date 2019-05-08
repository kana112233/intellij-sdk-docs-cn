---
title: Publishing Plugins with Gradle
---

配置Gradle支持后，您可以自动构建插件并将其部署到[JetBrains插件存储库](https://plugins.jetbrains.com)。

为此，您需要已将插件发布到插件存储库。

有关详细信息，请参阅[发布插件]指南(../../basics/getting_started/publishing_plugin.md)。


### 2.0添加您的帐户凭据


要将插件部署到插件存储库，您首先需要提供JetBrains帐户凭据。

我们将描述三个选项:仅使用Gradle属性，使用环境变量并使用Gradle任务的参数。


####使用Gradle属性文件

您可以将凭据存储在[Gradle属性](https://docs.gradle.org/current/userguide/build_environment.html#sec:gradle_configuration_properties)中。

至关重要的是，不要将这些凭据检入源代码管理中。


为此，请将以下信息放在项目根目录下的`gradle.properties`文件中，或者放在`GRADLE_HOME/gradle.properties`中。


```
intellijPublishUsername=YOUR_USERNAME_HERE
intellijPublishPassword=YOUR_PASSWORD_HERE
```

然后在`build.gradle`文件的`publishPlugin`任务中引用这些值:


```groovy
publishPlugin {
    username intellijPublishUsername
    password intellijPublishPassword
}
```

如果在项目的根目录中放置`gradle.properties`文件，请确保版本控制工具忽略此文件。

例如在Git中，您可以将以下行添加到`.gitignore`文件中:


```
gradle.properties
```

####使用环境变量


或者，可能稍微安全一些，因为您不会意外地将您的凭据提交给git，您可以通过环境变量“ORG_GRADLE_PROJECT_intellijPublishUsername”和“ORG_GRADLE_PROJECT_intellijPublishPassword”提供您的凭据。


例如，您可以通过在运行配置中提供它们来本地运行`publishPlugin`任务。

为此，创建一个Gradle运行配置(如果尚未完成)，选择Gradle项目，指定`publishPlugin`任务，然后添加提到的环境变量。


请注意，您仍需要在Gradle属性中放置一些默认值(可以为空)，否则您将收到编译错误。


####为Gradle任务提供参数


与使用环境变量类似，您也可以将凭证作为参数传递给Gradle任务。

您需要提供参数`-Dorg.gradle.project.intellijPublishUsername = myusername -Dorg.gradle.project.intellijPublishPassword = mypassword`，例如通过命令行或将其放入运行配置的参数中。


请注意，在这种情况下，您仍需要在Gradle属性中添加一些默认值。


### 2.1配置你的插件


gradle-intellij-plugin提供了许多[配置选项](https://github.com/JetBrains/gradle-intellij-plugin#configuration)，用于自定义Gradle如何构建插件。

其中最重要的是`版本`。

默认情况下，如果在构建脚本中修改`version`，Gradle插件将自动更新`plugin.xml`文件中的`<version>`。
 
 
Gradle插件还将更新`plugin.xml`文件中的`<idea-version since-build = ... />`值以匹配`intellij.version`，有效直到当前主要版本的最后一个版本
但是，您可以通过将`intellij.updateSinceUntilBuild`选项设置为`false`来禁用此功能。


```groovy
plugins {
    // Make sure to check for the latest version at https://plugins.gradle.org/plugin/org.jetbrains.intellij
    // You can also subscribe to releases at https://github.com/JetBrains/gradle-intellij-plugin/releases
    id 'org.jetbrains.intellij' version '0.4.8'
}

intellij {
    version '2018.3'
    pluginName 'idear'
    intellij.updateSinceUntilBuild false //Disables updating since-build attribute in plugin.xml
}

group 'com.jetbrains'
version '1.2' // Update me!
```

使用包含上述代码段的构建脚本运行`gradle runIde`时，Gradle将从[Snapshot](https://www.jetbrains.com/intellij-repository/snapshots)下载相应版本的IntelliJ IDEA(时间) 
(基于)或[发布](https://www.jetbrains.com/intellij-repository/releases)(基于版本)存储库，配置插件沙箱，安装插件，并启动IDE的新实例。

此任务可以直接从命令行运行，无需任何先前的工具帮助。


### 2.3部署插件


部署插件的第一步是确认它是否正常工作。

您可能希望通过[从磁盘安装插件](https://www.jetbrains.com/help/idea/managing-plugins.html)在目标IDE的新实例上验证这一点。
一旦您确信插件按预期工作，请确保插件版本已更新，因为JetBrains插件存储库不会接受具有相同版本的多个工件。
要将新版本的插件部署到JetBrains插件存储库，请执行以下Gradle命令:


```bash
gradle publishPlugin
```

现在检查插件的最新版本是否出现在[Plugin Repository](https://plugins.jetbrains.com/)上。

如果已成功部署，则当前在符合条件的IntelliJ平台版本上安装了插件的任何用户将在下次重新启动时收到可用的新更新通知。


您还可以通过配置`publishPlugin.channels`属性将插件部署到您选择的发布通道。

例如:


```groovy
publishPlugin {
    channels 'beta'
}
```

当为空时，这将使用默认的插件存储库，可供所有[JetBrains插件存储库](https://plugins.jetbrains.com/)用户使用。

但是，您可以发布到任意命名的频道。

这些非默认发布通道被视为单独的存储库，用于所有意图和目的。

使用非默认发布渠道时，用户需要添加新的[自定义插件存储库](https://www.jetbrains.com/help/idea/managing-plugins.html#repos)来安装插件。

例如，如果指定`publishPlugin.channels'canary'`，则用户需要添加`https://plugins.jetbrains.com/plugins/canary/list`存储库来安装插件并接收更新。

热门频道名称包括:


*`alpha`:https://plugins.jetbrains.com/plugins/alpha/list

*`beta`:https://plugins.jetbrains.com/plugins/beta/list

*`eap`:https://plugins.jetbrains.com/plugins/eap/list


有关可用配置选项的更多信息，请参见[intellij gradle插件的文档](https://github.com/JetBrains/gradle-intellij-plugin/blob/master/README.md#publishing-dsl)。


