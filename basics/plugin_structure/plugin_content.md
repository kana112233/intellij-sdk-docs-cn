---
title: Plugin Content
---

组织插件内容有两种可能的方法:


1.一个插件,由一个放在plugins目录中的`.jar`文件组成:


存档应包含配置文件(`META-INF/plugin.xml`),
    
插件图标文件(`META-INF/pluginIcon * .svg`),
    
以及实现插件功能的类.
配置文件
    
指定插件名称,描述,版本,供应商,支持的IntelliJ IDEA版本,
    
插件组件,操作和操作组以及操作用户界面放置.
    
有关插件图标SVG文件的更多信息,请参见[插件图标文件](plugin_icon_file.md)页面.


   ```
   .IntelliJIDEAx0/
     plugins/
       sample.jar/
         com/foo/...
         ...
         ...
         META-INF/
           plugin.xml
           pluginIcon.svg
           pluginIcon_dark.svg
   ```

2.位于lib文件夹中的`.jar`文件中的插件文件:


   ```
   .IntelliJIDEAx0/
     plugins/
       Sample/
         lib/
           libfoo.jar
           libbar.jar
           Sample.jar/
             com/foo/...
             ...
             ...
             META-INF/
               plugin.xml
               pluginIcon.svg
               pluginIcon_dark.svg
   ```

`lib`文件夹中的所有jar都会自动添加到类路径中.


