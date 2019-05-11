# 摘要

* [简介](welcome.md)
* [IntelliJ平台](intro/intellij_platform.md)
    * [有助于IntelliJ平台](basics/platform_contributions.md)
    * [IntelliJ平台编码指南](basics/intellij_coding_guidelines.md)
* [IntelliJ平台SDK](intro/about.md) 
    * [关键主题](intro/key_topics.md) 
    * [贡献SDK](CONTRIBUTING.md)
      * [SDK文档风格指南](intro/sdk_style.md)
      * [SDK代码示例指南](intro/sdk_code_guidelines.md) 
    * [行为准则](CODE_OF_CONDUCT.md)
* [获得帮助](intro/getting_help.md)

## 第一部分 - 插件
* [简介](basics.md)
    * [插件类型](basics/types_of_plugins.md)
* [入门](basics/getting_started.md) 
    * [使用Gradle](tutorials/build_system.md)  
        * [Gradle入门](tutorials/build_system/prerequisites.md)  
        * [发布您的插件](tutorials/build_system/deployment.md) 
    * [使用DevKit](basics/getting_started/using_dev_kit.md) 
        * [设置开发环境](basics/getting_started/setting_up_environment.md)  
        * [创建插件项目](basics/getting_started/creating_plugin_project.md)  
        * [创建动作](basics/getting_started/creating_an_action.md)  
        * [运行和调试插件](basics/getting_started/running_and_debugging_a_plugin.md)  
        * [部署插件](basics/getting_started/deploying_plugin.md)  
        * [发布插件](basics/getting_started/publishing_plugin.md)
* [自定义插件存储库](basics/getting_started/update_plugins_format.md)
* [插件结构](basics/plugin_structure.md) 
    * [插件内容](basics/plugin_structure/plugin_content.md) 
    * [插件类加载器](basics/plugin_structure/plugin_class_loaders.md) 
    * [插件组件](basics/plugin_structure/plugin_components.md) 
    * [插件扩展和扩展点](basics/plugin_structure/plugin_extensions_and_extension_points.md)
    * [插件动作](basics/plugin_structure/plugin_actions.md) 
    * [插件服务](basics/plugin_structure/plugin_services.md) 
    * [插件配置文件](basics/plugin_structure/plugin_configuration_file.md) 
    * [插件图标](basics/plugin_structure/plugin_icon_file.md) 
    * [插件依赖项](basics/plugin_structure/plugin_dependencies.md)
* [IntelliJ平台工件存储库](reference_guide/intellij_artifacts.md)
* [Kotlin插件开发](tutorials/kotlin.md)
* [内部操作菜单](reference_guide/internal_actions/internal_actions_intro.md)
    * [启用内部模式](reference_guide/internal_actions/enabling_internal.md)
    * [内部行动](reference_guide/internal_actions/interal_actions_menu.md) 
    * [UI工具](reference_guide/internal_actions/internal_ui_sub.md) 
    * [UI Inspector](reference_guide/internal_actions/internal_uii.md) 
    * [Laf Defaults](reference_guide/internal_actions/internal_ui_lafd.md)
* [IDE开发实例](basics/ide_development_instance.md)
* [插件开发常见问题](faq.md)

## 第二部分 - 基础平台
* [基础知识](platform/fundamentals.md)  
    * 组件模型  
    * 处置者
    * [线程](basics/architectural_overview/general_threading_rules.md) 
        * 后台任务
    * [消息传递基础设施](reference_guide/messaging_infrastructure.md)
    * 查询和查询执行程序
* [用户界面组件](user_interface_components/user_interface_components.md) 
    * [工具窗口](user_interface_components/tool_windows.md) 
    * [对话框](user_interface_components/dialog_wrapper.md)
    * [Popups](user_interface_components/popups.md)
    * [通知](user_interface_components/notifications.md)
    * [文件和类选择器](user_interface_components/file_and_class_choosers.md)
    * [编辑器组件](user_interface_components/editor_components.md)
    * [列表和树控件](user_interface_components/lists_and_trees.md)
    * [其他Swing组件](user_interface_components/misc_swing_components.md) 
    * [图标和图像](reference_guide/work_with_icons_and_images.md)
    * [配色方案管理](reference_guide/color_scheme_management.md)
    * [Kotlin UI DSL](user_interface_components/kotlin_ui_dsl.md)
* [UI主题](reference_guide/ui_themes/themes_intro.md)
    * [创建UI主题](reference_guide/ui_themes/themes.md)
    * [自定义UI主题](reference_guide/ui_themes/themes_customize.md)
    * [添加方案和图像](reference_guide/ui_themes/themes_extras.md)
    * [公开主题元数据](reference_guide/ui_themes/themes_metadata.md)
* [操作](basics/action_system.md)
    * [动作教程](tutorials/action_system.md) 
        * [1. 注册动作](tutorials/action_system/working_with_custom_actions.md)  
        * [2. 分组动作](tutorials/action_system/grouping_action.md)
* 设置
    * [持久化组件状态](basics/persisting_state_of_components.md)
    * [持久敏感数据](basics/persisting_sensitive_data.md)
    * 编辑设置
* [文件](basics/architectural_overview/files.md)
    * [虚拟文件系统](basics/virtual_file_system.md)
    * [虚拟文件](basics/architectural_overview/virtual_file.md)
* [文件](basics/architectural_overview/documents.md)
* [编辑](reference_guide/editors.md)
    * [编辑基础知识](tutorials/editor_basics.md)
        * [1.使用文本](tutorials/editor_basics/working_with_text.md)
        * [2.编辑协调系统位置和偏移](tutorials/editor_basics/coordinates_system.md) 
        * [3.处理编辑器事件](tutorials/editor_basics/editor_events.md)
    * [多个插入符号](reference_guide/multiple_carets.md)
* [运行配置](basics/run_configurations.md) 
    * [运行配置管理](basics/run_configurations/run_configuration_management.md) 
    * [执行](basics/run_configurations/run_configuration_execution.md)
    * [运行配置教程](tutorials/run_configurations.md)
* [版本控制系统](reference_guide/vcs_integration_for_plugins.md)
    * 差异
    * 当地历史
* 任务和上下文
* [本地化指南](reference_guide/localization_guide.md)
* 图表

## 第三部分 - 项目模型
* [简介](basics/project_structure.md)
* [项目](reference_guide/project_model/project.md)
    * [项目向导](reference_guide/project_wizard.md)
    * [项目向导教程](tutorials/project_wizard.md)
        * [向项目向导添加新步骤](tutorials/project_wizard/adding_new_steps.md)  
        * [支持模块类型](tutorials/project_wizard/module_types.md)
    * [框架](tutorials/framework.md)
* [模块](reference_guide/project_model/module.md)
* [SDK](reference_guide/project_model/sdk.md)
* [Library](reference_guide/project_model/library.md)
* [Facet](reference_guide/project_model/facet.md)
* [外部系统集成](reference_guide/frameworks_and_external_apis/external_system_integration.md)

## 第四部分 - PSI
* [什么是PSI?](basics/architectural_overview/psi.md)
* [PSI文件](basics/architectural_overview/psi_files.md)
* [文件视图提供程序](basics/architectural_overview/file_view_providers.md)
* [PSI Elements](basics/architectural_overview/psi_elements.md)
* [导航PSI](basics/architectural_overview/navigating_psi.md)
* [参考资料](basics/architectural_overview/psi_references.md)
* [修改PSI](basics/architectural_overview/modifying_psi.md)
* [PSI Cookbook](basics/psi_cookbook.md)
* [索引和PSI存根](basics/indexing_and_psi_stubs.md) 
    * [基于文件的索引](basics/indexing_and_psi_stubs/file_based_indexes.md)  
    * [存根索引](basics/indexing_and_psi_stubs/stub_indexes.md)
* 元素模式
* 统一AST
* [XML DOM API](reference_guide/frameworks_and_external_apis/xml_dom_api.md)

## 第五部分 - 特点
* 导航
    * 转到符号
* 编辑
    * 代码完成
    * 模板  
        * [实时模板](tutorials/live_templates.md)
            * [1.添加实时模板支持](tutorials/live_templates/template_support.md)
        *文件模板 
        *环绕模板
    * QuickDoc
    * [意图](tutorials/code_intentions.md)
* 分析
    * 注释器
    * [检查](tutorials/code_inspections.md)
        * 个人资料
        * 范围
        * 抑制亮点
        * 结构搜索
* 重构
* 项目视图
    * [修改项目视图结构](tutorials/tree_structure_view.md)
* 单元测试
* [Build System](reference_guide/project_model/build_system.md)
    * [External Builder API和插件](reference_guide/frameworks_and_external_apis/external_builder_api.md)

## 第六部分 - 测试

* [测试插件](basics/testing_plugins.md)
    * [测试和夹具](basics/testing_plugins/tests_and_fixtures.md)
    * [轻型和重型测试](basics/testing_plugins/light_and_heavy_tests.md)
    * [测试项目和测试数据目录](basics/testing_plugins/test_project_and_testdata_directories.md)
    * [编写测试](basics/testing_plugins/writing_tests.md)
    * [测试突出显示](basics/testing_plugins/testing_highlighting.md)

## 第七部分 - 自定义语言
* [自定义语言支持](reference_guide/custom_language_support.md)
    * [注册文件类型](reference_guide/custom_language_support/registering_file_type.md)
    * [实现Lexer](reference_guide/custom_language_support/implementing_lexer.md)
    * [实现 Parser and PSI](reference_guide/custom_language_support/implementing_parser_and_psi.md)
    * [语法突出显示和错误突出显示](reference_guide/custom_language_support/syntax_highlighting_and_error_highlighting.md)
    * [参考文献和解决方案](reference_guide/custom_language_support/references_and_resolve.md)
    * [代码完成](reference_guide/custom_language_support/code_completion.md)
    * [查找用法](reference_guide/custom_language_support/find_usages.md)
    * [重命名重构](reference_guide/custom_language_support/rename_refactoring.md)
    * [安全删除重构](reference_guide/custom_language_support/safe_delete_refactoring.md)
    * [代码格式化程序](reference_guide/custom_language_support/code_formatting.md)
    * [代码检查和意图](reference_guide/custom_language_support/code_inspections_and_intentions.md)
    * [结构视图](reference_guide/custom_language_support/structure_view.md)
    * [Surround With](reference_guide/custom_language_support/surround_with.md)
    * [转到课程并转到符号](reference_guide/custom_language_support/go_to_class_and_go_to_symbol.md)
    * [文档](reference_guide/custom_language_support/documentation.md)
    * [其他次要功能](reference_guide/custom_language_support/additional_minor_features.md)
    * 做资源管理器
    * 参数信息
    * 参数提示
* [自定义语言支持教程](tutorials/custom_language_support_tutorial.md)
    * [1.先决条件](tutorials/custom_language_support/prerequisites.md)
    * [2.语言和文件类型](tutorials/custom_language_support/language_and_filetype.md)
    * [3.语法和解析器](tutorials/custom_language_support/grammar_and_parser.md)
    * [4.Lexer和Parser定义](tutorials/custom_language_support/lexer_and_parser_definition.md)
    * [5.语法荧光笔和颜色设置页面](tutorials/custom_language_support/syntax_highlighter_and_color_settings_page.md)
    * [6.PSI助手和实用程序](tutorials/custom_language_support/psi_helper_and_utilities.md)
    * [7.注释](tutorials/custom_language_support/annotator.md)
    * [8.行标记提供者](tutorials/custom_language_support/line_marker_provider.md)
    * [9.完成贡献者](tutorials/custom_language_support/completion_contributor.md)
    * [10.参考文献贡献者](tutorials/custom_language_support/reference_contributor.md)
    * [11.查找用户提供商](tutorials/custom_language_support/find_usages_provider.md)
    * [12.折叠生成器](tutorials/custom_language_support/folding_builder.md)
    * [13.转到符号贡献者](tutorials/custom_language_support/go_to_symbol_contributor.md)
    * [14.结构视图工厂](tutorials/custom_language_support/structure_view_factory.md)
    * [15.格式化器](tutorials/custom_language_support/formatter.md)
    * [16.代码样式设置](tutorials/custom_language_support/code_style_settings.md)
    * [17.评议](tutorials/custom_language_support/commenter.md)
    * [18.快速修复](tutorials/custom_language_support/quick_fix.md)
* [测试自定义语言插件](tutorials/writing_tests_for_plugins.md)
    * [1.测试先决条件](tutorials/writing_tests_for_plugins/tests_prerequisites.md)
    * [2.解析测试](tutorials/writing_tests_for_plugins/parsing_test.md)
    * [3.完成测试](tutorials/writing_tests_for_plugins/completion_test.md)
    * [4.注释器测试](tutorials/writing_tests_for_plugins/annotator_test.md)
    * [5.格式化程序测试](tutorials/writing_tests_for_plugins/formatter_test.md)
    * [6.重命名测试](tutorials/writing_tests_for_plugins/rename_test.md)
    * [7.折叠测试](tutorials/writing_tests_for_plugins/folding_test.md)
    * [8.查找用法测试](tutorials/writing_tests_for_plugins/find_usages_test.md)
    * [9.评论者测试](tutorials/writing_tests_for_plugins/commenter_test.md)
    * [10.参考测试](tutorials/writing_tests_for_plugins/reference_test.md)
* 注入语言
* 构建系统
* 编译器
* 调试器

## 第八部分 - 产品特定
* [与多个产品的兼容性](basics/getting_started/plugin_compatibility.md)
* [Build Number Ranges](basics/getting_started/build_number_ranges.md)
* [IntelliJ IDEA](products/idea.md)
    * [Tomcat集成](reference_guide/tomcat_integration.md)
    * [Spring API](reference_guide/frameworks_and_external_apis/spring_api.md)
* [PhpStorm](products/phpstorm/phpstorm.md)
    * [设置环境](products/phpstorm/setting_up_environment.md)
    * [PHP Open API](products/phpstorm/php_open_api.md)
    * [现有第三方插件](products/phpstorm/existing_plugins.md)
* [PyCharm](products/pycharm.md)
* DataGrip
* Android Studio
* 骑士


## 第九部分 - 自定义IDE
* 构建自己的IDE
* 许可

## Part X  - 插件存储库
* [简介](plugin_repository/index.md)
* [API Reference](plugin_repository/api/api_reference.md)
    * [插件上传](plugin_repository/api/plugin_upload.md)
    * [插件列表](plugin_repository/api/plugins_list.md)
    * [插件详细信息](plugin_repository/api/plugin_details.md)
    * [插件更新下载](plugin_repository/api/plugin_download_update.md)
    * [Maven接口](plugin_repository/api/maven_interface.md)
    * [插件开发者列表](plugin_repository/api/plugin_developers.md)
* [插件建议](plugin_repository/feature_extractor.md)
* [自定义发布渠道](plugin_repository/custom_channels.md)

## 附录I  - 资源

* [有用的链接](appendix/resources/useful_links.md)
* [咨询](appendix/resources/consulting.md)

## 附录II  -  API变更

* [不兼容的API更改](reference_guide/api_changes_list.md)
    * [2019.*](reference_guide/api_changes/api_changes_list_2019.md)
    * [2018.*](reference_guide/api_changes/api_changes_list_2018.md)
    * [2017.*](reference_guide/api_changes/api_changes_list_2017.md)
    * [2016.*](reference_guide/api_changes/api_changes_list_2016.md)


* [值得注意的API更改](reference_guide/api_notable/api_notable.md)
    * [2019.*](reference_guide/api_notable/api_notable_list_2019.md)
    * [2018.*](reference_guide/api_notable/api_notable_list_2018.md)