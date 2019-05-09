---
title: Plugin Repository Maven Interface
---

您可以使用https://plugins.jetbrains.com/maven`上提供的Maven界面从[plugin repository](https://plugins.jetbrains.com)下载插件更新.


URL格式如下:


```
https://plugins.jetbrains.com/maven/<channel>/com/jetbrains/plugins/<plugin_xml_id>/<version>/<plugin_xml_id>-<version>.<extension>
```

哪里


* ** plugin_xml_id **是插件的单个updade页面右侧或* plugin.xml *中指定的唯一插件XML ID;


* **版本**是完整更新版本;


* **频道**是发布更新的发布频道(空频道表示默认*稳定*频道);


* ** extension **是一个插件更新包扩展名(* .jar *或* .zip *).


例如.
下载* [VueJs插件版本1.0.1](https://plugins.jetbrains.com/plugin/8057-vue-js/update/23034)*您应该发送请求[https://plugins.jetbrains.com 
/maven/com/jetbrains/plugins/vue.js/1.0.1/vue.js-1.0.1.jar](https://plugins.jetbrains.com/maven/com/jetbrains/plugins/vue.js/1.0.1/vue.js-1.0.1.jar).


此外,您可以使用Gradle或Maven将插件检索为maven依赖项:


* ** groupId **是** \ <channel> .com.jetbrains.plugins **或** com.jetbrains.plugins ** for * Stable * channel;


* ** artifactId **是** pluginXmlId **(pluginXmlId被指定为插件的个人更新页面右侧的*插件XML ID *参数);


** **的build.gradle


```
repositories {
  maven { url 'https://plugins.jetbrains.com/maven' }
}
 
dependencies {
  compile 'com.jetbrains.plugins:<plugin_xml_id>:<version>' // for the plugin from Stable channel
  compile '<channel>.com.jetbrains.plugins:<plugin_xml_id>:<version>' // for the plugin from non-default channel
}
```

(请参阅上面的参数说明)


