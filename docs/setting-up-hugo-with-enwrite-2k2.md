# 用 Enwrite 设置 Hugo

> 原文：<https://dev.to/ozgurodun/setting-up-hugo-with-enwrite-2k2>

TLDR；这是我第一次写博客，告诉我如何用 hugo 建立我的新博客。

嗨，
这将是我在这里的第一篇帖子，我不太喜欢写东西，但是看到人们在 dev.to 上发帖和分享经验让我想到写我喜欢的东西和我做的事情。希望你会喜欢。
第一篇文章将是关于建立我的新网站。

# 故事部分

我拥有这个域名[ozgurodun.com](https://ozgurodun.com)已经很长时间了，我试着建立过几次博客，但是从来没有提交过任何内容。正如我以前说过的，我是一个懒于写作的人。dev.to 开放后，我阅读了许多开发者的帖子(非常感谢 Ben 和 Jess 提供了这个伟大的平台)，并开始考虑分享。所以我决定再开一次博客。这次我至少一个月写一次:)

# 技术

很长一段时间，我想建立一个静态网站。为此，我使用了 Hugo T1，这是构建静态网站最快的框架。
[![Hugo](img/2fe3530c36cc2733e247537dc99baf6f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z-oOBVo_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0l3jm5fsnqbg8k9ozk1p.png) 
Hugo 很容易部署，速度也很快，但最终我不想使用 cli 或 git webhooks 来添加新帖子。所以我搜索了一下，找到了 [enwrite](https://github.com/zzamboni/enwrite) 。这将作为我的网站 cms 的工作。Enwrite 与 evernote 帐户同步，搜索特定的笔记本和“已发布”标签，然后将这些笔记作为降价文件或 html 文件保存到您的公共目录中。我会用一个 debian 安装的 vps，把这些粘在一起。

# 设置

### 雨果

首先，我开始安装 hugo，它有大量的文档[在这里](https://gohugo.io/documentation)。我没有从源代码开始构建，所以我下载了 deb [这里](https://github.com/gohugoio/hugo/releases)T4】安装并构建一个新站点:

```
$ dpkg -i hugo_0.30.2_Linux-64bit.deb
$ hugo new site my_site_name 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个新的站点模板(文件夹和基本配置文件)。并且`$ hugo server -D`会启动一个服务器用于测试。和-D 选项也将构建草稿文件。

### 主题

之后，我转到[雨果主题](https://themes.gohugo.io/)并选择“介绍主题”。安装主题也很容易。
在你新的 my_site_name 目录下创建一个名为“themes”的目录，然后

```
$ git clone https://github.com/hivickylai/hugo-theme-introduction.git introduction 
```

Enter fullscreen mode Exit fullscreen mode

每个 hugo 主题都有不同的特性，有些内置了对 disqus 的支持，有些是最小化设计，有些是作品集，有些是单页简历。你可以选择更适合你的。感谢 Vicky Lai 在这个库中准备了一个示例站点，我查看她的示例配置文件来设置我的站点。我玩了设置，并在布局文件中做了一些小改动。我在看代码的时候顺便赢了一块饼干。感谢:)
[![Vicky Lai's cookie](img/a7f57980e5c216d2a1596325e8722fce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XT7DXRdR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6gyzofozzhehdid1j2jl.png)

### 写下

要安装 enwrite，我需要 apt-get 安装 rubygems，然后:

```
$ gem install enwrite 
```

Enter fullscreen mode Exit fullscreen mode

安装很容易，但在玩了一会儿 enwrite 后，我意识到上次提交 enwrite 是在 3 年前，在那之后 hugo 进行了许多更新。So easy enwrite 与最新的 hugo 不兼容。犹豫了一会儿后，我决定克隆 enwrite 库并修复这个问题。幸运的是，代码被很好地记录下来，并且修复非常小；我改变并构建了 enwrite。如果赞博尼接受:D，我会提出第一个请求(迟到总比不到好)。

```
$ enwrite -n my_notebook -t published -o ~/my_new_site 
```

Enter fullscreen mode Exit fullscreen mode

这将在 evernote 的“我的 _ 笔记本”中搜索“已发布”的笔记标签，并将它们输出到 my_new_site 文件夹中。为了测试，我写了一些“lorem ipsum”帖子，并运行带有“- rebuild_all”的 enwrite 命令

### Cron

最后，我做了两个 cron 作业，一个是 hugo 的，一个是 enwrite 的，每五分钟他们会搜索新的笔记并建立到公共文件夹。

# 最终

这就是我建立博客的基本方式。如果你有任何问题，可以在评论区提问。祝你有愉快的一天！！:D