---
title: Contributing to the IntelliJ Platform SDK
---

本文档描述了我们对开源IntelliJ Platform SDK文档和示例代码的贡献指南.
在开始向SDK提供内容之前,请仔细阅读本页以及[行为准则](/CODE_OF_CONDUCT.md)和[许可证](https://github.com/JetBrains/intellij-sdk-docs/blob/master/LICENSE.txt)文档.
有关为IntelliJ平台本身做出贡献的信息,请访问[贡献IntelliJ平台](/basics/platform_contributions.md).


在创作SDK内容和提交Pull请求之前,请先了解一些有用的信息.
* 虚拟列表项
{:TOC}
 

## 设置文档构建环境

该站点通过[Jekyll](https://jekyllrb.com)运行,这是一个流行的静态站点生成器,用Ruby编写.它可以在本地托管,以确保任何更改都是正确的.设置完成后,运行网站就像调用“rake preview”一样简单.

或者,该站点也可以托管在[Docker容器](https://www.docker.com)中.在Mac和Windows上,这意味着该站点托管在虚拟机中. Docker维护这个容器,根据[`Dockerfile`](Dockerfile)中的指令构建它.构建映像时会自动安装所有依赖项(Ruby等),从而减少了手动配置步骤. Docker镜像也用于构建[已发布网站](https://www.jetbrains.org/intellij/sdk/docs/index.html),因此它是一个已知的工作环境.


### 使用Docker开发文档

请按照以下步骤使用Docker:

* 首先,使用[Docker Toolbox](https://www.docker.com/docker-toolbox)安装Docker.
* 在Windows和Mac上,启动Docker主机虚拟机(启动“Docker Quickstart终端”或运行“Kitematic”.有关更多信息,请参阅[docker.com]上的入门指南(https://www.docker.com)
细节).
* 将[`intellij-sdk-docs`](https://github.com/JetBrains/intellij-sdk-docs)repo克隆到本地机器,并[初始化`sdkdocs-template`子模块](#a-word-on-submodules).
* 将当前目录更改为git仓库的父目录.
* 运行`docker build -t intellij-sdk-docs .`从当前文件夹构建docker镜像,并为其指定标签`intellij-sdk-docs`.  
* 请注意,这必须从设置了各种`DOCKER_ *`环境变量的命令提示符运行,例如Docker Quickstart Terminal.
* 运行`docker run -p 4000:4000 -v $ PWD:/usr/src/app intellij-sdk-docs`.
该命令将:   
* 启动名为`intellij-sdk-docs`的docker容器.   
* 将Docker容器中的端口4000转发到docker客户端上的端口4000.

> **注意**对于Windows和Mac,这意味着docker容器的端口4000被转发到docker虚拟机的端口4000,而不是“localhost”.对于Linux,docker客户端是主机,因此`localhost:4000`被转发到docker容器上的端口4000. 
>
> 要从Windows或Mac命中容器的端口4000,必须点击docker客户端(虚拟机)的IP地址.使用`docker-machine ip default`获取docker客户端的IP地址.使用`X.X.X.X:4000`命中虚拟机中的客户端,然后将其映射到容器的端口4000.  
> 
>或者,修改虚拟机的设置以自动将端口4000转发到“localhost”.
有关详细信息,请参阅[博客文章](https://acaird.github.io/computers/2014/11/16/docker-virtualbox-host-networking).
 
    
*挂载当前目录(`$ PWD`是Unix样式的环境变量.您可以在Windows上使用`％CD％`,或指定完整路径)作为docker容器内的`/usr/src/app`. 
docker镜像将`intellij-sdk-docs` repo视为文件夹`/usr/src/app`.

> **注意**如果在Windows上运行MSYS bash脚本(例如“Docker Quickstart终端”),则需要正确转义本地文件夹的路径,否则MSYS环境会将路径转换为标准Windows路径,
并导致错误,例如`invalid value "C:\\Users\\...;C:\\Program Files\\Git\\usr\\src\\app" for flag -v`.要解决此问题,请在整个路径前加上双斜杠,例如:
`-v //c/Users/...`,或`docker run -p 4000:4000 -v /$PWD:/usr/src/app intellij-sdk-docs`(注意`$ PWD之前的前导斜杠
`).

    *运行Dockerfile的`CMD`指令中的命令来执行:
  *`rake bootstrap`,确保安装所有先决条件,
  *`rake preview`,它构建网站并开始托管它.
*最后,您可以访问[http://localhost:4000/intellij/sdk/docs/](http://localhost:4000/intellij/sdk/docs/)或使用IP访问新创建的站点docker客户端虚拟机的地址(请参阅上面的注释).

###在本地开发文档


要构建文档站点,您需要:


* Ruby 2  -  Jekyll是一个Ruby应用程序.

* Ruby 2 DevKit(适用于Windows) - 需要编译一些Jekyll的依赖项,并且需要安装DevKit.

*`gem install bundler`  - 该站点使用[Bundler](https://bundler.io)来管理repo中的gem依赖项,而不是全局安装到本地操作系统.
运行此命令以全局安装Bundler工具集.


#### 苹果系统


macOS已经安装了Ruby.
所需的唯一步骤是:


*`gem install bundler`


#### Windows


*安装[Ruby 2](https://rubyinstaller.org)和[Ruby 2 DevKit](http://rubyinstaller.org/downloads/)(其中一个宝石需要构建本机组件)
    
*安装DevKit后,请确保编辑`config.yml`文件以指向Ruby安装


如果您使用[Chocolatey](https://chocolatey.org)(Windows的包管理器),则此安装更容易:


* `choco install ruby​​`
* `choco install ruby​​2.devkit`
    * 安装DevKit后,请确保编辑`config.yml`文件以指向Ruby安装.
    * 默认情况下,这是`C:\tools\DevKit\config.yml`
    * 添加行`- C:\tools\ruby21`(包括前导减号)

> **注意**在运行下面列出的“rake bootstrap”步骤之前,请运行DevKit中的`devkitvars.bat`文件. 例如. `C:\tools\DevKit\devkitvars.bat`

### 引导文档构建环境

1. 确保已安装Bundler  - `gem install bundler`.
2. 在Windows上,确保已在当前命令提示符下运行`devkitvars.bat`文件(例如`c:\tools\DevKit\devkitvars.bat`).
3. 克隆文档站点.
4. 初始化并更新`sdkdocs-template`子模块 - `git submodule init`和`git submodule update`
5. `rake bootstrap` - 这使用Bundler下载所有必需的宝石.
6. `rake preview` - 这将构建网站,并将其托管在本地网络服务器中.

### 构建和预览网站

要构建和测试网站,请运行`rake preview`.这将使用提供的配置构建站点并托管它.托管站点的URL显示在屏幕上,取决于`_config.yml`中定义的`baseurl`字段.

> **注意**必须使用`localhost`作为主机名,_NOT_ 0.0.0.0,否则无法加载字体.

## Documentation Repository Submodules
`sdkdocs-template`目录实际上是一个Git子模块,它包含一个私有`webhelp-template`存储库的子模块.
`sdkdocs-template`存储库包含构建脚本以及允许站点运行的编译和缩小的JS和CSS.
私有的`webhelp-template`存储库包含构建JS和CSS的代码.
它目前是封闭源,但计划是在某些时候使它成为开源,在这种情况下,很可能会合并两个存储库.

克隆后,需要初始化和更新子模块:

```sh
git submodule init
git submodule update
```

初始化创建一个`.gitmodules`文件,在`sdkdocs-template`文件夹中注册一个子模块并检出文件.
请注意,当repo作为子模块添加时,它不会获得`.git`文件夹,而是获取指向`.git`文件夹的实际位置的`.git`文件.


子模块可以使用常规git命令更新,例如`git pull`.
它可以使用`git checkout`切换到不同的分支,并且需要使用git命令将当前检出的修订版本的任何更改提交回主存储库.
子模块最初是在特定版本中克隆的,而不是branch.update的一部分


如果对子模块进行了更改,则应在分支上创建克隆,并发送Pull请求.
可以进行更改并提交,托管存储库需要提交指向子模块当前版本的指针.


如果`sdkdocs-template`有任何问题,请[提出问题](https://github.com/JetBrains/sdkdocs-template/issues).


## 创建IntelliJ Platform SDK内容

欢迎提供对IntelliJ Platform SDK的内容贡献.

请从[GitHub](https://github.com/JetBrains/intellij-sdk-docs)下载或克隆开源SDK项目,进行添加或更改,并提交拉取请求.

在创建或更改内容之前,请参阅以下指南:

* [SDK文档样式指南](intro/sdk_style.md).
  
本指南根据Markdown语法描述了文档约定.
  
始终使用站点的[预览](#building-and-previewing-the-site)测试文档更改.

* [SDK代码示例指南](intro/sdk_code_guidelines.md).
  
本文档中描述了代码示例组织,项目设置和命名约定的约定.
  
始终通过构建和测试SDK代码示例来测试代码更改.


