---
title: SDK
---

每个项目都使用软件开发工具包(SDK).
对于Java项目,SDK称为JDK(Java Development Kit). 
SDK确定用于构建项目的API库.
如果您的项目是多模块,则默认情况下项目SDK对于项目中的所有模块都是通用的. 
(可选)您可以为每个模块配置各个SDK.
有关SDK的更多信息,请参阅IntelliJ IDEA Web帮助中的[SDK](https://www.jetbrains.com/help/idea/working-with-sdks.html).


##获取项目SDK信息


有关项目SDK的主要信息可以通过[ProjectRootManager.java](upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/ProjectRootManager.java)访问,如下例所示


```java
Sdk projectSdk = ProjectRootManager.getInstance(project).getProjectSdk();
```

##获取和设置项目SDK属性


*获取项目级SDK


  ```java
  Sdk projectSDK = ProjectRootManager.getInstance(project).getProjectSdk();
  ```

* To get the project level SDK name:
 
  ```java 
  String projectSDKName = ProjectRootManager.getInstance(project).getProjectSdkName();
  ```

* To set the project level SDK:
 
  ```java
  ProjectRootManager.getInstance(project).setProjectSdk(Sdk jdk);
  ```

* To set the project level SDK name:
 
  ```java
  ProjectRootManager.getInstance(project).setProjectSdkName(String name);
  ```
  
See the following [code sample](../../code_samples/project_model/src/com/intellij/tutorials/project/model/ProjectSdkAction.java) to get more familiar with SDK manipulation tool set.

##使用自己的SDK


要创建自己的SDK,您需要创建一个类extends [SdkType](upsource:///platform/lang-api/src/com/intellij/openapi/projectRoots/SdkType.java),将`saveAdditionalData`留空,
将它注册到`com.intellij.sdkType`扩展点.


要使您的SDK设置保持不变,您应该覆盖`setupSdkPaths`并通过`modificator.commitChanges()`保存您的设置:


```java
@Override
public boolean setupSdkPaths(@NotNull Sdk sdk, @NotNull SdkModel sdkModel) {
    SdkModificator modificator = sdk.getSdkModificator();
    modificator.setVersionString(getVersionString(sdk));
    modificator.commitChanges(); // save
    return true;
}
```

要让用户选择SDK,请参阅[ProjectJdksEditor](upsource:///java/idea-ui/src/com/intellij/openapi/projectRoots/ui/ProjectJdksEditor.java).


但是,建议不要在非IDEA IDE中使用“SDK”.
虽然“SDK”在大多数JB产品中都可用,但`ProjectJdksEditor`是IntelliJ特有的,使得围绕“SDK”的操作变得困难.

管理“SDK”设置的最佳推荐方法是创建[`CustomStepProjectGenerator`](upsource:///platform/lang-impl/src/com/intellij/ide/util/projectWizard/CustomStepProjectGenerator.java)

在[`PersistentStateComponent`]中实现和保存设置(/basics/persisting_state_of_components.md).


