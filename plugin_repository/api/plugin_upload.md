---
title: Upload Plugin API
---
 
Please note that maximum allowed plugin size is *200 MB*.

你应该在[JetBrains Hub]创建一个[** hubPermanentToken **](https://www.jetbrains.com/help/hub/Manage-Permanent-Tokens.html)(https://hub.jetbrains.com/
users/me？tab = authentification)并在Scope中指定插件存储库.


![Hub Token](img/hub_token.png)


其他参数:


* ** pluginId **是插件的数字ID,可以从插件存储库URL中检索.
例如
[Scala](https://plugins.jetbrains.com/plugin/1347-scala)插件ID为* 1347 *. 
(*可以使用* pluginXmlId *)

* ** pluginXmlId **是plugin.xml中指定为<id>的插件的唯一标识符.
可以在插件的单个更新页面右侧和plugin.xml中找到* Plugin XML ID *参数. 
(*可以使用* pluginId *代替)

* **频道**是发布更新的发布频道(空频道表示默认*稳定*频道)(可选)


## POST


**使用pluginId **


提供文件作为文件内容.
卷曲命令模板:


```
curl -i --header "Authorization: Bearer <hubPermanentToken>" -F pluginId=<pluginId> -F file=@<path to plugin .jar/.zip file> -F channel=<channel> https://plugins.jetbrains.com/plugin/uploadPlugin
```

卷曲命令示例:


```
curl -i --header "Authorization: Bearer perm:qwertyasdfghzxcvb" -F pluginId=5047 -F file=@Go-0.11.1197.zip -F channel=nightly https://plugins.jetbrains.com/plugin/uploadPlugin
```

**使用pluginXmlId **


提供文件作为文件内容.
卷曲命令模板:


```
curl -i --header "Authorization: Bearer <hubPermanentToken>" -F xmlId=<pluginXmlId> -F file=@<path to plugin .jar/.zip file> -F channel=<channel> https://plugins.jetbrains.com/plugin/uploadPlugin
```

卷曲命令示例:


```
curl -i --header "Authorization: Bearer perm:qwertyasdfghzxcvb" -F xmlId=ro.redeul.google.go -F file=@Go-0.11.1197.zip -F channel=nightly https://plugins.jetbrains.com/plugin/uploadPlugin
```
## .NET

您可以使用* NuGet CLI *的* push *命令上传.NET的插件.

您应该使用[hubPermanentToken](https://www.jetbrains.com/help/hub/Manage-Permanent-Tokens.html)

使用范围内的插件存储库,如上所示.


推送命令模板:


```
nuget push <path to plugin .nupkg file> <hubPermanentToken> -Source https://plugins.jetbrains.com/
```

推送命令示例:


```
nuget push angularjs.1.9.0.nupkg perm:qwertyasdfghzxcvb -Source https://plugins.jetbrains.com/
```

**组态**


NuGet的配置位于`％AppData％\ NuGet \ NuGet.Config`(Windows)中,

`〜/.config/NuGet/NuGet.Config`或`〜/.nuget/NuGet/NuGet.Config`(Mac/Linux).

您可以使用[NuGet CLI命令](https://docs.microsoft.com/en-us/nuget/reference/nuget-config-file)修改此文件.


例如,您可以为源设置密钥并将* plugins site *添加到默认推送源:


```
# Set hub token for plugins site
nuget setApiKey perm:qwertyasdfghzxcvb -Source https://plugins.jetbrains.com/

# Set plugins site as default for push
nuget config -set defaultPushSource=https://plugins.jetbrains.com/

# Push if the key and the default source are set
nuget push angularjs.1.9.0.nupkg
```
In addition to the fact that you can change NuGet's default configuration, 
you can apply your own configuration file using `ConfigFile` option in *push* command. 
For example:

```
nuget push angularjs.1.9.0.nupkg -ConfigFile ~/my.Config
```

要获取有关NuGet配置的更多信息,请参阅[此页面](https://docs.microsoft.com/en-us/nuget/consume-packages/configuring-nuget-behavior).




