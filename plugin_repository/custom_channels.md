---
title: Custom Release Channels
---

## 将插件更新上传到自定义发布渠道


您可以通过插件存储库Web界面,[Gradle插件](../tutorials/build_system/deployment.md)或[API](api/plugin_upload.md)将插件部署到您选择的特定频道.


当为空时,这将使用默认的插件存储库通道(* Stable *),可供所有JetBrains插件存储库用户使用,但是,您可以发布到任意命名的通道.


使用非默认发布渠道时,用户需要添加新的自定义插件存储库来安装插件.


在插件存储库中,所有频道都将显示在插件页面上:


![自定义渠道](img/plugin_repository_custom_channels.png)


您可以在插件*编辑*页面(以及通过Gradle插件和API)上添加自定义渠道并将更新上传到频道:


![添加频道](img/plugin_repository_add_custom_channel.png)


## 在基于IntelliJ平台的IDE中配置自定义通道


使用非默认发布通道时,基于IntelliJ Platform的IDE用户需要添加新的[自定义插件存储库](https://www.jetbrains.com/idea/help/managing-enterprise-plugin-repositories.html)
从指定的频道安装插件.


例如,如果指定“eap”作为发布通道,则用户需要添加“https://plugins.jetbrains.com/plugins/eap/list”存储库来安装插件并接收更新.
所有意图和目的都将这些通道视为单独的存储库.


热门频道名称包括:


* `eap`:https://plugins.jetbrains.com/plugins/eap/list

* `alpha`:https://plugins.jetbrains.com/plugins/alpha/list

* `beta`:https://plugins.jetbrains.com/plugins/beta/list


也可以托管[Enterprise Plugin Repository](https://www.jetbrains.com/idea/help/adding-plugins-to-enterprise-repositories.html),在这种情况下,URL结构将取决于您的
托管地址.
有关详细信息,请参阅[IntelliJ IDEA文档](https://www.jetbrains.com/idea/help/managing-plugins.html).


作为一个选项,您可以添加一个自定义存储库,指定插件ID(插件ID是插件的数字ID,可以从插件存储库URL中检索.例如[Scala](https://plugins.jetbrains.com/plugin) 
/1347-scala)插件ID为* 1347 *):


```
https://plugins.jetbrains.com/plugins/<channel>/<pluginId>
```
(e.g. `https://plugins.jetbrains.com/plugins/eap/1347`)

有关如何在基于IntelliJ平台的IDE中指定自定义插件存储库的更多信息,请参阅[管理企业插件存储库](https://www.jetbrains.com/help/idea/managing-plugins.html)帮助文章.
从自定义发布渠道接收插件和更新:


![在IntelliJ IDEA中添加自定义插件存储库](img/intellij_custom_plugin_repository.png)


## 频道优先通知


请注意,自定义存储库优先于默认通道.
当未提供自定义通道名称时,更新将上载到默认通道,默认通道也称为稳定通道或释放通道.
这意味着来自自定义渠道(例如EAP渠道)的插件/更新会“覆盖”来自主存储库(默认渠道)的插件/更新.


如果用户订阅了EAP自定义渠道,他们将无法在没有渠道的情况下看到插件更新(因此他们将无法在默认渠道中看到新的更新).


因此,如果您希望用户获得此更新,建议您在将自定义频道添加到默认(稳定)频道时将其添加到自定义频道.


这种行为是暂时的,将来会进行大量的重新设计.


