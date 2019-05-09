---
title: Library
---

库是模块所依赖的已编译代码(例如JAR文件)的存档.

IntelliJ平台支持三种类型的库:


* **模块库**:库类仅在此模块中可见,库信息记录在模块* .iml文件中.

* **项目库**:库类在项目中可见,库信息记录在项目* .ipr文件或.idea/libraries中.

* **全局库**:库信息在applicationLibraries.xml文件中记录到`<User Home>/.IntelliJIdea/config/options`目录中.
全局库与项目库类似,但对于不同的项目是可见的.


有关库的更多信息,请参阅

[Library](https://www.jetbrains.com/help/idea/working-with-libraries.html).


##访问库和罐子


包

[libraries](upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/libraries)

提供使用项目库和jar的功能.


###如何获取模块所依赖的库列表？


要获取模块所依赖的库列表,请使用“OrderEnumerator.forEachLibrary”方法.

以下代码段演示了如何执行此操作:


```java
final List<String> libraryNames = new ArrayList<String>();
ModuleRootManager.getInstance(module).orderEntries().forEachLibrary(library -> {
  libraryNames.add(library.getName());
  return true;
});
Messages.showInfoMessage(StringUtil.join(libraryNames, "\n"), "Libraries in Module");
```

此示例代码输出`module`模块所依赖的库列表.


###如何获取所有库的列表？


要管理应用程序和项目库的列表,[LibraryTable](upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/libraries/LibraryTable.java)

使用了类.
通过调用`LibraryTablesRegistrar.getInstance().getLibraryTable()`来访问应用程序级库表的列表,

而项目级库表的列表可以通过`LibraryTablesRegistrar.getInstance().getLibraryTable(Project)`来访问.

一旦你有一个`LibraryTable`,你可以通过调用`LibraryTable.getLibraries()`来获取它.


To get the list of all module libraries defined in a given module, use the following API:
```java
OrderEntryUtil.getModuleLibraries(ModuleRootManager.getInstance(module));
```

###如何获取图书馆内容？


`Library`类提供了`getUrls`方法,您可以使用它来获取库包含的源根和类的列表.
为了澄清,请考虑以下代码段:


```java
StringBuilder roots = new StringBuilder("The " + lib.getName() + " library includes:\n");
roots.append("Sources:\n");
for (String each : lib.getUrls(OrderRootType.SOURCES)) {
  roots.append(each).append("\n");
}
roots.append("Classes:\n");
for (String each : lib.getUrls(OrderRootType.CLASSES)) {
  strRoots.append(each).append("\n");
}
Messages.showInfoMessage(roots.toString(), "Library Info");
```

在此示例代码中,`lib`是[Library](upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/libraries/Library.java)类型.


###如何创建库？


要创建库,您需要执行以下步骤:


*获取[写入操作](../../basics/architectural_overview/general_threading_rules.md#readwrite-lock)
  
*获取要添加库的库表.
根据库级别,使用以下其中一项:
      
*`LibraryTablesRegistrar.getInstance().getLibraryTable()`
      
*`LibraryTablesRegistrar.getInstance().getLibraryTable(Project)`
      
*`ModuleRootManager.getInstance(module).getModifiableModel().getModuleLibraryTable()`
  
*通过调用`LibraryTable.createLibrary()`创建库
  
*将内容添加到库中(见下文)
  
*对于模块级库,提交由`ModuleRootManager.getInstance(module).getModifiableModel()`返回的可修改模型.
  

对于模块级库,您还可以在[ModuleRootModificationUtil]中使用简化的API(upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/ModuleRootModificationUtil.java)

用于添加具有单个API调用的库的类.
您可以在[示例插件]中找到使用这些API的示例(https://github.com/JetBrains/intellij-sdk-docs/blob/master/code_samples/project_model/src/com/intellij/tutorials/project/
模型/ModificationAction.java).


###如何将内容添加到库或修改它？


要添加或更改库的根,您需要执行以下步骤:


*获取[写入操作](../../basics/architectural_overview/general_threading_rules.md#readwrite-lock)
  
*使用`Library.getModifiableModel()`获取库的**可修改模型**
  
*使用诸如`Library.ModifiableModel.addRoot()`之类的方法来执行必要的更改
  
*使用`Library.ModifiableModel.commit()`提交模型.
  

###如何向模块添加库依赖项？


在写入操作下使用`ModuleRootModificationUtil.addDependency(module,library)`.


###检查属于图书馆


[ProjectFileIndex](upsource:///platform/projectModel-api/src/com/intellij/openapi/roots/ProjectFileIndex.java)接口实现了许多方法,可用于检查指定的文件是否属于项目库
课程或图书馆资源.

您可以使用以下方法:


*检查指定的虚拟文件是否是编译的类文件使用


  ```java
  ProjectFileIndex.isLibraryClassFile(virtualFile)
  ```
* To check if a specified virtual file or directory belongs to library classes use

  ```java
  ProjectFileIndex.isInLibraryClasses(virtualFileorDirectory)
  ```
* To check if the specified virtual file or directory belongs to library sources use

  ```java
  ProjectFileIndex.isInLibrarySource(virtualFileorDirectory)
  ```

请参阅以下[代码示例](https://github.com/JetBrains/intellij-sdk-docs/blob/master/code_samples/project_model/src/com/intellij/tutorials/project/model/ProjectFileIndexSampleAction.java)

看看如何应用上述方法.


有关库的更多详细信息,请参阅此处

[代码示例](https://github.com/JetBrains/intellij-sdk-docs/blob/master/code_samples/project_model/src/com/intellij/tutorials/project/model/LibrariesAction.java)


