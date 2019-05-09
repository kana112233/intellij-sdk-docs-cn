---
title: PyCharm
---

## PyCharm插件开发人员应该使用哪个API？


### Platform Open API


可以在插件中安全地使用标准的Platform Open API模块集中的API.
这些模块中定义的公共符号具有生命周期,并且在删除之前将被标记为已弃用.


这些模块[在`CommunityRepositoryModules.groovy`文件中定义](upsource:///platform/build-scripts/groovy/org/jetbrains/intellij/build/CommunityRepositoryModules.groovy).


### PyCharm Open API


** python-psi-api **和** python-openapi **模块中定义的符号也是公共API,并且被认为是稳定的.


###其他API


使用其他模块中定义的类和方法需要您自担风险,应注意使用您希望支持的任何版本的PyCharm来测试您的插件.


