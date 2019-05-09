---
title: External System Integration
---

＃目的


此页面提供*外部系统*子系统的高级概述.


#Rationale


有多个项目管理系统([maven](https://maven.apache.org/),[gradle](http://www.gradle.org/),[sbt](http://www.scala-sbt.org/)etc)并且很高兴在IDE上支持它们.
幸运的是,从集成的角度来看,它们都提供了类似的设施:


*从外部系统配置(pom.xml,build.gradle等)构建IDE项目;

*提供可用任务列表;

*允许执行特定任务;

* ......


这意味着我们可以将外部系统特定逻辑和一般IDE处理分开. 
*'外部系统'*子系统提供简单的api,用于包装外部系统和可扩展的IDE特定处理逻辑.


＃ 项目管理


##项目数据域


**一般**

外部系统包装器需要能够根据给定的外部系统配置构建项目信息.
该信息是根据 [DataNode](upsource:///platform/external-system-api/src/com/intellij/openapi/externalSystem/model/DataNode.java), [Key](upsource:///platform/external-system-api/src/com/intellij/openapi/externalSystem/model/Key.java) and [ProjectEntityData](upsource:///platform/external-system-api/src/com/intellij/openapi/externalSystem/model/project/ProjectEntityData.java).

![数据管理部](/reference_guide/img/data-node.png)


这里* DataNode *类只是目标数据的持有者(数据类型由* Key *定义).
可以在有向图中组织多个DataNode对象,其中每个边都标识父子关系.


例如,简单的单模块项目可能如下所示:
 

![DataNode示例](/reference_guide/img/data-node-example.png)


**后果**

IDE提供了一组内置的* Key *和* ProjectEntityData *,但任何外部系统集成或第三方插件开发人员都可以通过定义自己的* Key *和* ProjectEntityData *并将其存储在子项中来增强项目数据
适当的* DataNode *.


##管理项目数据


我们需要处理项目数据是建立在外部系统配置的基础上的.
这里有[ProjectDataService](upsource:///platform/external-system-api/src/com/intellij/openapi/externalSystem/service/project/manage/ProjectDataService.java).
这是一种知道如何管理特定* ProjectEntityData *的策略.
例如,当我们想要从外部模型导入项目时,我们可以从引用项目信息的顶级* DataNode *开始,然后使用相应的服务导入其数据.


可以通过*'externalProjectDataService'*扩展名定义自定义服务.


好处是我们可以在这里分离项目解析和管理.
这意味着可以为特定技术引入一组* DataNode *,* Key *和* ProjectDataServices *,然后每个外部系统集成可以在必要时使用它来构建相应的数据.


##从外部模型导入


IntelliJ平台为此提供标准API.
即,[ProjectImportBuilder](upsource:///projectImport/ProjectImportBuilder.java)和[ProjectImportProvider](upsource:///projectImport/ProjectImportProvider.java).
在* template method * pattern上构建了两个类 -  [AbstractExternalProjectImportBuilder](upsource:///platform/external-system-impl/src/com/intellij/openapi/externalSystem/service/project/wizard/AbstractExternalProjectImportBuilder.java)[AbstractExternalProjectImportProvider](upsource:///platform/external-system-impl/src/com/intellij/openapi/externalSystem/service/project/wizard/AbstractExternalProjectImportProvider.java).
它们可能是子类,具体实现应该在IoC描述符(plugin.xml)中注册.


以下是gradle集成插件的示例:


<projectImportProvider implementation =“org.jetbrains.plugins.gradle.service.settings.GradleProjectImportProvider”/>
    
<projectImportBuilder implementation =“org.jetbrains.plugins.gradle.service.settings.GradleProjectImportBuilder”/>


请注意,[AbstractExternalProjectImportBuilder](upsource:///platform/external-system-impl/src/com/intellij/openapi/externalSystem/service/project/wizard/AbstractExternalProjectImportBuilder.java)构建于“外部系统设置”之上
控制.


##自动导入


当外部项目的配置文件被修改时,可以配置外部系统集成以自动刷新项目结构.
基本上,最终用户应在外部系统设置中检查相应的框:


![自动导入](/reference_guide/IMG /使用-自动import.png)


内置支持仅涵盖链接外部项目的根配置文件.
但是,可能存在这样的情况:特定外部项目具有另一个配置文件,这些配置文件也会影响生成的项目结构(例如,它可能是一个多项目,其中每个子项目都有自己的配置文件).
这就是为什么通过使目标外部系统实现(* ExternalSystemManager *)实现* ExternalSystemAutoImportAware *来增强该处理的原因.
这允许提供自定义逻辑,用于将文件修改事件映射到受其影响的目标外部项目.


**注意:** * ExternalSystemAutoImportAware.getAffectedExternalProjectPath()*经常被调用,这就是为什么它应该尽快返回控制. 
Helper * CachingExternalSystemAutoImportAware *类可能用于缓存,即* ExternalSystemManager *实现* ExternalSystemAutoImportAware *可以有一个字段,如*'new CachingExternalSystemAutoImportAware(new MyExternalSystemAutoImportAware())* *委托* ExternalSystemAutoImportAware.getAffectedExternalProjectPath()*调用它.


＃设置


一般的想法是所有外部系统设置控件都由[ExternalSettingsControl]的实现表示(upsource:///platform/external-system-impl/src/com/intellij/openapi/externalSystem/service/settings/ExternalSettingsControl.java)
接口.
还有外部系统项目本地设置和全局外部系统设置.
因此,基本上特定的外部系统设置UI如下所示:


![配置](/reference_guide/IMG/configurable.png)


建议从[AbstractExternalProjectSettingsControl](upsource:///platform/external-system-impl/src/com/intellij/openapi/externalSystem/service/settings/AbstractExternalProjectSettingsControl.java)进行扩展,以实现项目级设置控制,因为它已经
处理其中一些.


类似的方法用于提供“从外部系统导入”UI  - 实现有望扩展[AbstractImportFromExternalSystemControl](upsource:///platform/external-system-impl/src/com/intellij/openapi/externalSystem/service/settings/AbstractImportFromExternalSystemControl.java)并且它没有链接外部项目列表但是目标外部项目路径控制:


![从外部系统导入](/reference_guide/img/import.png)


