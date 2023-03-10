# 如何建立一个机器人数字黑客空间

> 原文：<https://dev.to/ladvien/how-to-build-a-robotics-digital-hackerspace-33nk>

### 一些 HTML

<u>到底为什么？</u>

[![LeavingLMR.jpg](img/1ad547ba5e5ff6a152e24e8dc46337df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wh25kAAv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/LeavingLMR.jpg)

哇，我已经有一段时间没有用基于 Drupal 的文本编辑器写东西了。感觉像家一样。

老实说，我不打算进入“为什么”建立这个网站。也许我以后会那样做。这么说吧，有一种动力去重建我们被迫离开的家。

<u>怎么眨眼了？</u>

启动服务器:

第一件事是决定服务器。我没有太多(嗯，任何)启动服务器的经验。但是在读了一点书，检查了银行账户，找到了适合 Drupal 站点的东西之后，我选择了 [Linode](https://www.linode.com/) 。

到目前为止肯定开心。他们的商业模式似乎是没有虚饰的快速运转的 Linux 服务器——如果你知道你在做什么的话。当然，我没有。尽管如此，他们有很多相当最新的演练。

以下是我用来启动该服务器的演练(按顺序):

1.  [入门](https://www.linode.com/docs/getting-started)
2.  [保护您的服务器](https://www.linode.com/docs/security/securing-your-server)
3.  [托管网站](https://www.linode.com/docs/websites/hosting-a-website)
4.  [安装 Drupal 7](https://www.linode.com/docs/websites/cms/managing-web-content-with-drupal-7)

这几乎是创建这个网站所需要的所有信息的基础。几个警告:

```
sudo apt-get install php5 php-pear sudo apt-get install php5-mysql 
```

Enter fullscreen mode Exit fullscreen mode

需要替换为:

```
sudo apt-get install php php-pear sudo apt-get install php-mysql 
```

Enter fullscreen mode Exit fullscreen mode

这将安装最新版本的 PHP，对我来说，那是 PHP7。

另外，PHP7 的安装目录是不同的。这很重要，因为 php.ini 文件需要编辑。在 7 中可以找到:

```
/etc/php/7.0/cli 
```

Enter fullscreen mode Exit fullscreen mode

最后要做的事情是将 Drupal 7 文件重定位到 Apache 可以提供这些文件的目录中。

```
cd /var/www/html sudo wget https://ftp.drupal.org/files/projects/drupal-7.50.zip 
```

Enter fullscreen mode Exit fullscreen mode

如果正确遵循了所有脚本-博客，那么当在浏览器中输入站点的 web IP 时，您应该会看到以下内容:

[![Drupal_On_Linode.png](img/6214fe3a62cc5cc256c27b4a246a68ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lE0M5k-4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/Drupal_On_Linode.png)

设计目标:

目标。有他们真好。思考一下我对新机器人之家的期望，以下是一些看起来至关重要的概念:

1.  一个开放的地方，任何人都可以表达自己的想法，最少可行的审查。
2.  反应灵敏。不仅移动友好，而且优化了快速查看信息。
3.  Shoutbox。必须有一个。
4.  所见即所得和丰富内容编辑器:
    *   Iframes
    *   图像框
    *   图像存储系统
    *   代码突出显示，包括我们最喜欢的语言(以及 Bdk6 先生的 Ada)
5.  我们老房子的主要特征:
    *   收集
    *   冷却区
    *   论坛
    *   等等，我已经说过了。
6.  会员要求的新功能:
    *   内容分级
    *   会员积分
    *   维基网
    *   任何我们能想到的东西

主题:

这个主题有三个层次:

1.  [AdaptiveThemes](https://www.drupal.org/project/adaptivetheme%5C) 被选为主
2.  [花冠](https://www.drupal.org/project/corolla)越过它
3.  从它上面走过。

在/var/www/html/sites/all/themes/corolla/目录中有一个名为 corolla.info 的文件，里面添加了一个对 css/corolla _ overrides . CSS .

[![Screenshot 2016-10-10 18.59.30.png](img/0a0890d84e2e20c304f6dbba94fe6b48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7pD_io0_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/Screenshot_2016-10-10_18.59.30.png)

的引用，然后在/var/www/html/sites/all/themes/corolla/CSS 目录中制作了 corolla_override.css 文件，其中包含了几个粗略的 CSS 片段:

```
 /* The title was originally set to -5px (weird) */
    #site-name a {
      letter-spacing: 0px;
    }

    .wrap-code {
      overflow-x: scroll;
    }

    /* These hide some uncessary shoutbox labels. */
    .shoutbox-interval-msg {
      visibility: hidden;
      display: none;
    }
    #edit-nick{
      visibility: hidden;
      display:none;
    } 
```

Enter fullscreen mode Exit fullscreen mode

关于配色方案。调色板可以在 Footheme“颜色”部分进行编辑。这需要启用颜色(Drupal 核心)模块。调色板是通过使用 [Pictaculous](http://www.pictaculous.com/) 拍摄俄罗斯革命艺术的图像来选择的。这意味着捕捉一位更好地理解[色彩-情感](https://designshack.net/articles/graphics/the-science-behind-color-and-emotion/)联系的艺术家选择的感觉调色板。

[![](img/989c2107d06b0181d9c52eeb3779f748.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--I-viNHMJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/a1.jpg)

字体

标志选择

模块:

[管理工具:](https://www.drupal.org/project/admin_tools)

“管理工具”是[管理](http://drupal.org/project/admin)模块的附加模块，为选定的角色提供侧边栏导航。管理工具模块增加了清除缓存、运行 cron 和更新的功能和快速访问，很像[管理菜单](http://drupal.org/project/admin_menu)。”

我*讨厌*缓存错误。妈-眨眼缓存！

[管理菜单](https://www.drupal.org/project/admin_menu):

管理工具大大加快了开发速度。它基本上简化了管理菜单，所以 4 级项目暴露在一次点击。

博客(核心)

允许 Drupal 站点像一个好的旧博客一样运行——对我们来说，这允许多创作内容和管理。

块(核心)

允许用户界面的块设计。

[混沌工具](https://www.drupal.org/project/ctools)

这是对其他模块(很多其他模块)的依赖。

[CKEditor](https://www.drupal.org/project/ckeditor) [![Screenshot 2016-09-24 20.49.03.png](img/eb292817ab8d5e70cb676e7eae345e25.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--it9A82xu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/Screenshot_2016-09-24_20.49.03.png)

CKEditor 是 Drupal 博客包的核心。它是用来创建这个帖子的编辑器。然而，当试图安装时，它进行了最激烈的斗争。实际上，这不公平。不是编辑器的问题，而是代码高亮显示的问题。代码高亮显示允许:

```
 for(int i = 0; i < marioTouchesRobots; i++){
        aHackerSpiritDies();
    } 
```

Enter fullscreen mode Exit fullscreen mode

我将列出设置本文使用的 CKEditor 的步骤，然后讨论一些陷阱，这些陷阱最终会耗费大量开发时间。

使用代码片段和高亮显示来设置 CKEditor 的步骤:

1.  下载[cke editor-WYSIWYG HTML 编辑器](https://www.drupal.org/project/ckeditor)模块。
2.  启用 CKEditor 模块。
3.  转到配置-> cke editor->编辑 cke editor 全局配置文件
4.  将“CKEditor 的路径”设置为//cdn . CK editor . com/4 . 5 . 4/full-all。这将使用内容交付网络来提供 CKEditor JavaScript。它还能让你不用管理插件就能访问很多插件。另一种选择是在本地服务器上拉一份拷贝——这要麻烦得多。
5.  进入配置- >CKEditor - >Profile X - >Edit(注意，X =用户写博客时可以选择的文本编辑配置文件。这些可以在内容内容创作->文本格式下管理)。
6.  转到基本设置。在这里，添加这个特定 CKEditor 配置文件应该影响的所有文本格式。![Screenshot 2016-10-02 10.43.11.png](img/375a12196a8453ef6125eff8dd877c61.png)
7.  在“安全”下，确保“总是为 CKEditor 运行安全过滤器”被启用(应该是默认的)。
8.  在编辑器外观下，直接进入复选框标题“用于插入代码片段的插件”并启用它。
9.  此外，启用所需的其他 CKEditor 插件。注意，有更多的插件，但这些是通过内容交付网络提供的。
10.  滚动到末尾，点击保存。现在，回到配置-> cke editor-> Profile X-> Edit。直接进入编辑器外观。应该有一个新按钮可用
11.  将代码片段按钮添加到“当前工具栏”
12.  这将启用 CKEditor 并提供代码片段按钮。
13.  下载[高亮 js](https://www.drupal.org/project/highlightjs) Drupal 模块。这应该安装在模块目录中
14.  导航到 var/www/html/sites/all/libraries 文件夹，创建一个名为“highlightjs”的目录，切换到该目录。
15.  高亮 js 模块依赖于实际的[高亮 js css](https://highlightjs.org/) 库。在/var/www/html/sites/all/libraries/highlight js 文件夹中下载一个包。
16.  在这里解压。
17.  发出命令“sudo mv highlight . pack . js highlight js”。这是必需的，否则高亮模块无法找到库。
18.  以及命令“sudo chmod 666 highlightjs”。
19.  转到模块仪表板并启用高亮显示 JS 语法。<u>不要</u>启用高亮 JS 过滤器。
20.  打开模块/var/www/html/sites/all/modules/cke editor $
21.  sudo nano ckeditor.config.js 标牌
22.  在编辑器的下拉框中添加您想要显示的每种语言。冒号左边的部分应该匹配 HighlighJS 语言代码。之间的部分将显示在 CKEditor 下拉列表中。在添加支持的语言时，这里有一个很好的参考——[支持的 HighlightJS 语言](http://highlightjs.readthedocs.io/en/latest/css-classes-reference.html)(但不包括自定义语言，比如 Arduino)。完成后不要忘记保存。

```
 config.codeSnippet_languages = {
            php: 'PHP',
            python: 'Python',
            arduino: 'Arduino',
            c: 'C'
        }; 
```

Enter fullscreen mode Exit fullscreen mode

1.  ![Overflow.png](img/bbe468a871a89f36c7ee67a383cf8340.png)highlight js 模块存在文本脱离 div 的问题。花了很长时间才找到罪魁祸首。显然，每当 HighlightJS 模块呈现 CKEditor 生成的 HTML 时，都会导致这种情况。
2.  请转到/var/www/html/sites/all/modules/highlight js
3.  sudo nano highlight_js.css 类型
4.  输入以下样式并保存:

```
 .field-items {
        width: 100%;
    } 
```

Enter fullscreen mode Exit fullscreen mode

应该就是这样了。几句警告的话。确保您没有启用 HighlightJS 过滤器。这实际上会对 `block.  This causes >, <, and & to show as ">, &lt, &" respectively.  This simple little issue took a lot of development time to solve--given the manual was lacking.` 中的 HTML 实体进行双重编码

颜色
评论
上下文链接
仪表盘
数据库日志
实体 API
字段
字段 SQL 存储
字段 UI
文件
过滤器
五星
论坛
帮助
荧光笔 JS 过滤器
荧光笔 JS 语法荧光笔
图片
IMCE
库
列表
菜单
模块过滤器
节点(核心)
编号
选项
覆盖
路径
轮询
RDF
搜索
Shoutbox
Shoutbox 补丁
统计
统计计数器
SysLog
系统(核心)
分类
文本(核心)
更新管理器
用户
用户点数
用户点数服务
视图
视图内容窗格【T44