# 本地机器上的 WordPress

> 原文：<https://dev.to/itscoderslife/wordpress-on-local-machine-33oa>

当我和一个朋友谈到学习 **wordpress 开发**时，他说你为什么现在想学习 wordpress？这是一项古老的技术。现在，每个人要么转向 T2 媒体，要么从头开始开发自己的博客。

这是真实的，现实的。有了 node、MEAN stack、Swift for server side 和类似的其他框架，开发者可以用最新的技术构建和运行自己的网站。WordPress 仍然在 php 和 MySQL 上工作——经典的 xAMP 栈。

> 为什么现在要学 wordpress？这是一项古老的技术。现在，每个人要么转向媒体，要么从头开始开发自己的博客。

这并不意味着我们应该停止学习仍然有效的旧技术。这些系统仍然运行良好，扩展性良好。与当今的任何新平台相比，它们都经过深思熟虑，设计得更好，更耐用。我的意思是脸书仍然由 php 驱动。

这并不意味着新技术在可扩展性方面不存在。可悲的事实是，大多数人永远没有机会去衡量。有些人从未见过天日之光，有些人能见到却几乎没有任何订阅者，博客的开发者也从未扩展过他们。

### 为什么我现在学这个？

学习和理解，实验，了解它的内部和需要做的地方，然后根据你的需要定制它。

> WP 为开始创建你自己的网站，主要是博客，提供了一个基础。*   Templates are available in large quantities or built and shared by yourself.*   Take the time to customize it according to your own needs, instead of building it from scratch.*   We can learn how various components are used.*   And architecture and patterns followed.*   Pattern design. It's always fun to learn old techniques.T15】

这就是我继续从 wordpress 开始的原因。作为第一步，我想安装 wordpress 并尝试一些主题和插件。但要安装，我需要一个服务器托管，也需要一个数据库托管。接下来我搜索了**如何在我的系统上本地安装 WordPress？**得到了结果。

它需要一个 xAMP 栈–**Apache-MySQL-PHP**无论是在 Linux、macOS 还是 Windows 上。我有一台苹果电脑。接下来是如何启动 Apache 服务器？在网上搜索了一下，找到了 T2 MAMP T3，这让你的任务变得很简单。MAMP 也适用于 macOS 和 windows，还有一个 [WAMP](http://www.wampserver.com/en/) 只适用于 Windows。

没有比免费拥有一台自己的服务器来做实验更好的方法了。这种完全自由的唯一最佳方式是在您的系统上本地安装它。

### 本地安装 WordPress:

*   下载 [MAMP](https://www.mamp.info/en/)
*   根据他们的站点安装 MAMP 或 WAMP
*   记住这是 T2。org 不是。com
*   解压下载的 wordpress 文件，你必须得到一个文件夹——**WordPress**
*   将 **wordpress** 文件夹复制到已安装的 MAMP 文件夹的 **htdocs** 文件夹中*
*   现在点击**启动服务器**
*   一旦加载微调器停止，您会看到 Apache 和 MySQL 前面的绿色，您的服务器正在运行
*   打开你最喜欢的浏览器，输入**[http://localhost:8888](http://localhost:8888)**然后回车
*   设置数据库–
    *   转到 *localhost/phpmyadmin*
    *   转到数据库，为表添加一个名称，然后单击创建
*   配置 WordPress
    *   现在打开一个浏览器标签，输入 *localhost:8888/wp-admin*
    *   跟着向导走
    *   当要求输入数据库名称、用户名、密码、主机
    *   默认情况下，用户名和密码都是“ *root*
    *   保持表格前缀不变，点击*提交*
    *   接下来点击*运行安装*
    *   给你的博客起一个名字，用户名密码和电子邮件 id
    *   点击安装 wordpress。成功！
    *   现在，当您登录时，您应该能够看到管理控制台

```
Congrats!! You are up and running wordpress instance locally 
```

*或者，你也可以设置 MAMP 发球的路线。启动 MAMP–转到首选项–Web 服务器选项卡–更改文档根文件夹。

### 何去何从？

我还没有这样做，但肯定下一步是设计你的网站和定制你的 wordpress 安装。这里有一个关于如何建立一个 wordpress 网站的视频系列

现在探索安装主题，创建几个主题和插件。在创建它们的同时，了解每样东西是如何匹配的将会很有趣。我刚刚在我的系统上安装了 WP，还没有探索所有这些。

这是我学习如何在本地安装 WordPress 的地方。