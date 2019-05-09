---
title: Module
---

_module_是一个独立的功能单元,可以独立运行,测试和调试.
模块包括源代码,构建脚本,单元测试,部署描述符等.


模块的关键组件是:
  
* **内容根**  - 属于该模块的文件的目录(源代码,资源等)
    
存储.
每个目录可以属于一个且只有一个模块;
共享内容根目录是不可能的
    
多个模块之间.
  
* **源根**  - 内容根可以在其下面有多个**源根**.
源根可以有不同的类型:
   
常规源根,测试源根,资源根等.在IntelliJ IDEA中,源根用作包层次结构的根
   
结构(源根目录下的Java类将位于根包中).
源根也可以用来
   
实现更细粒度的依赖性检查(常规源根目录下的代码不能依赖于测试下的代码
   
源根).
并非所有其他基于IntelliJ平台的IDE都使用源根.
  
* **订单输入**  - 模块的依赖关系,存储在有序列表中.
依赖可以作为参考
    
到[SDK](sdk.md),[library](library.md)或其他模块.
  
* ** [Facets](facet.md)**  - 特定于框架的配置条目列表.


除此之外,模块还可以存储其他设置,例如特定于模块的[SDK](sdk.md),编译输出路径

设置,等等.
插件可以通过创建构面或模块级组件来存储与模块关联的其他数据.


* IntelliJ Platform *提供了许多可用于处理模块的Java类和接口:


* [`ModuleManager`](upsource:///platform/projectModel-api/src/com/intellij/openapi/module/ModuleManager.java) abstract class.
* [`Module`](upsource:///platform/core-api/src/com/intellij/openapi/module/Module.java) interface.
* [`ModuleRootManager`](upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/ModuleRootManager.java) abstract class.
* [`ModuleRootModel`](upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/ModuleRootModel.java) interface.
* [`ModuleUtil`](upsource:///platform/lang-api/src/com/intellij/openapi/module/ModuleUtil.java) class.
* [`ModifiableModuleModel`](upsource:///platform/projectModel-api/src/com/intellij/openapi/module/ModifiableModuleModel.java) interface.
* [`ModifiableRootModel`](upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/ModifiableRootModel.java) interface.

本节讨论如何完成与模块管理相关的一些常见任务.


###如何获得项目包含的模块列表？


使用`ModuleManager.getModules()`方法.


###如何获取模块的依赖关系和类路径？


_Order entries_包括模块使用的SDK,库和其他模块.
使用* IntelliJ IDEA * UI,您可以在* Project Structure *对话框的[Dependencies](https://www.jetbrains.com/help/idea/dependencies-tab.html)选项卡上查看模块的订单条目
框.


要探索[模块依赖关系](https://www.jetbrains.com/help/idea/dependencies-tab.html),请使用[OrderEnumerator](upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/OrderEnumerator.java)类.


以下代码片段说明了如何获取模块的类路径(所有依赖项的类根):


```java
VirtualFile[] roots = ModuleRootManager.getInstance(module).orderEntries().classes().getRoots();
```

###如何获取模块使用的SDK？


使用`ModuleRootManager.getSdk()`方法.
此方法返回[Sdk](upsource:///platform/projectModel-api/src/com/intellij/openapi/projectRoots/Sdk.java)类型的值.


以下代码段说明了如何获取指定模块使用的SDK的详细信息:


```java
ModuleRootManager moduleRootManager = ModuleRootManager.getInstance(module);
Sdk SDK = moduleRootManager.getSdk();
String jdkInfo = "Module: " + module.getName() + " SDK: " + SDK.getName() + " SDK version: "
                 + SDK.getVersionString() + " SDK home directory: " + SDK.getHomePath();
```

###如何获得该模块直接依赖的模块列表？


使用`ModuleRootManager.getDependencies()`方法获取`Module`类型值的数组或`ModuleRootManager.getDependencyModuleNames()`以获取模块名称数组.
为了澄清,请考虑以下代码段:


```java
ModuleRootManager moduleRootManager = ModuleRootManager.getInstance(module);
Module[] dependentModules = moduleRootManager.getDependencies();
String[] dependentModulesNames = moduleRootManager.getDependencyModuleNames();
```

###如何获取依赖于该模块的模块列表？


使用`ModuleManager.getModuleDependentModules(module)`方法.


请注意,您还可以通过以下方式使用`ModuleManager.isModuleDependent`方法检查模块(* module1 *)是否依赖于另一个指定的模块(* module2 *):


```java
boolean isDependent = ModuleManager.getInstance(project).isModuleDependent(module1,module2);
```

###如何获取指定文件或PSI元素所属的模块？


*要获取指定文件所属的项目模块,请使用`ModuleUtil.findModuleForFile()`静态方法.


为了澄清,请考虑以下代码段:


```java
String pathToFile = "C:\\users\\firstName.LastName\\plugins\\myPlugin\src\MyAction.java";
VirtualFile virtualFile = LocalFileSystem.getInstance().findFileByPath(pathToFile);
Module module = ModuleUtil.findModuleForFile(virtualFile,myProject);
String moduleName = module == null ? "Module not found" : module.getName();
```

*要获取指定的[PSI元素](../../basics/architectural_overview/psi_elements.md)所属的项目模块,请使用`ModuleUtil.findModuleForPsiElement(psiElement)`方法.


###访问模块根目录


可以通过类[ModuleRootManager.java](upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/ModuleRootManager.java)访问有关模块根的信息.

例如,以下代码段显示了如何访问模块的内容根:


```java
VirtualFile[] contentRoots = ModuleRootManager.getInstance(module).getContentRoots();
```

###检查属于模块源根目录


要检查虚拟文件或目录是否属于模块源根目录,请使用`ProjectFileIndex.getSourceRootForFile`方法.
如果文件或目录不属于项目中任何模块的源根,则此方法返回null.


```java
VirtualFile moduleSourceRoot = ProjectRootManager.getInstance(project).getFileIndex().getSourceRootForFile(virtualFileOrDirectory);
```

##接收有关模块更改的通知


要接收有关模块更改的通知(要添加,删除或重命名的模块),

使用[message bus](/reference_guide/messaging_infrastructure.md)和`ProjectTopics.MODULES`主题:


```java
project.getMessageBus().connect().subscribe(ProjectTopics.MODULES, new ModuleListener() {
  @Override
  public void moduleAdded(@NotNull Project project, @NotNull Module module) {

  }
});
```

