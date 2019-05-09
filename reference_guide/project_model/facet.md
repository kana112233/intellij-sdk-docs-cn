---
title: Facet
---

facet表示与模块相关联的特定框架/技术的特定配置.

模块可以有多个方面.
例如. 
Spring特定配置存储在Spring facet中.

有关构面的更多信息,请参阅[添加对框架和技术的支持](https://www.jetbrains.com/help/idea/configuring-projects.html#add-support-for-frameworks-technologies)和帮助页面
IntelliJ IDEA Web帮助中的[Facets对话框](https://www.jetbrains.com/help/idea/facets.html).


##使用Facets


要访问模块的构面列表,请使用[FacetManager](upsource:///platform/lang-api/src/com/intellij/facet/FacetManager.java)

和[Facet](upsource:///platform/lang-api/src/com/intellij/facet/Facet.java)类.


