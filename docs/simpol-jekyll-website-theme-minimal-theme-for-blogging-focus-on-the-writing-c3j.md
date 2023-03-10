# Simpol Jekyll 网站主题-博客的最小主题，专注于写作。

> 原文：<https://dev.to/scottmathson/simpol-jekyll-website-theme-minimal-theme-for-blogging-focus-on-the-writing-c3j>

# 介绍 Simpol 主题

Simpol 主题是一个为 Jekyll 设计的简洁的网站主题。

这是一个免费的开源项目。

> ### Focus on writing and content.

[![Simpol Theme screenshot](img/cd43f4f759f8efdc335a6170b3cbee10.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0klC3rjE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://scottmathson.com/assets/img/blog/simpol-theme-screenshot.png)

Simpol 主题的灵感来自于我的个人网站之前的定制版本、模板和版本。

我对 Simpol 主题的灵感:

在设计和开发这个 Jekyll 主题时，我的一些灵感来自于像 Zen Habits 和贾斯廷·杰克逊的个人网站这样的网站，这些网站拥抱极简主义，真正专注于页面/帖子的写作和内容。

除了物理表现的灵感，我觉得 Simpol 的*简单和最小化设计*唤起了一种平静、组织和清晰的感觉。作为一名设计师，一名艺术家，我不断受到启发，不仅是“事物”，更重要的是感受和情感。

好吧…这听起来有点俗气，但这是真的，是真实的，是真实的。

查看这个模板的一个[现场演示，这里是](https://simpol.scottmathson.com/)。

## 惊鸿一瞥

首先，让我们来看看目前在这个主题上使用的宝石/插件——有意保持它的简单、轻量和快速加载。

```
plugins:
- jekyll-sitemap
- jekyll-seo-tag
- jekyll-feed
- jekyll-paginate 
```

Enter fullscreen mode Exit fullscreen mode

对于这个主题的用户来说，设置并开始根据你的喜好定制 Simpol 主题是非常容易的。几乎所有您需要的东西都在`_config.yml`文件中，这是初始设置的一站式商店。

我开发的主题有一些默认的内容，“开箱即用”，因为我不喜欢在用户购买/下载后向他们展示绝对最少的主题版本。你所看到的就是你所得到的(然后一些)- *没有噱头*。主题有`liquid` if/else 块，这些块有一些 Simpol 的默认文本内容，是自动填充的。

这里看一下主题附带的定制配置选项。一旦填写完毕，您在配置文件中写入的信息就会写入索引页。

```
#content for index.html
  #Add your own info, deleting the comments "#". This data automatically writes to the Index page's mini, about section.
greeting: #GREETING_GOES_HERE | default: Hello, I'm Simpol.
logo_img: #LOGO_IMG_SRC_GOES_HERE | default: /assets/img/simpol-theme-square-writing-logo.png
subgreeting_one: #SUBGREETING_ONE_GOES_HERE | default: Jekyll theme that's all about the content.
subgreeting_two: #SUBGREETING_TWO_GOES_HERE | default: Minimal, clean, simple blogging. 
```

Enter fullscreen mode Exit fullscreen mode

由于这个网站是用 Jekyll 建立的，所以它将整理下面的液体 if/then 块。主题的原始代码看起来不同。

```
<div class="top-headline-photo">
    <div class="sm-1-col md-2-col lg-2-col pull-left">
        <h1>Hello, I'm Simpol.</h1>
    </div><!-- end .sm-1-col lg-2-col pull-left -->
    <div class="sm-1-col md-2-col lg-2-col pull-right small-mobile-hide">
        <img src="/assets/img/simpol-theme-square-writing-logo.png" alt="Simpol Theme Index Logo">
    </div><!-- end. sm-1-col lg-2-col pull-right mobile-hide -->
</div><!-- end .top-headline-photo -->

<div class="sm-1-col md-2-col lg-2-col pull-left">
    <p>Jekyll theme that's all about the content.</p>
</div><!-- end .sm-1-col lg-2-col pull-left -->
<div class="sm-1-col md-2-col lg-2-col pull-right">
    <p>Minimal, clean, simple blogging.</p>
</div><!-- end. sm-1-col lg-2-col pull-right --> 
```

Enter fullscreen mode Exit fullscreen mode

帖子前面的问题真的不能再简单了，但也有足够的健壮性和 SEO 附加值。每篇文章都有(如果写出/选择)作者姓名、发表日期/时间、显示在主索引页面上的标题图像、文章类别(随后显示为网站上`/categories`页面上的类别和文章列表)等等。

当然，所有这些数据，包括标题、描述、日期/时间、作者等等，都包含在开放图形数据的元数据中，具有难以置信的 SEO 价值。

同样，Simpol 主题是绝对使用 schema.org 标记为您的整个网站和它的职位，告诉搜索引擎的职位是什么。

这里有一个基于 Jekyll 的 Simpol 主题的博客帖子的前沿问题。

```
--------
layout: post
title: "Simpol Blogging, Adding New Posts"
description: "How to add your first post. Simpol Theme blogging overview."
date: 2017-04-16 17:00:00
author: "Scott Mathson"
header-img: assets/img/posts/header-img/woman-typing-macbook.jpg

categories:
  - Tips/Tricks
-------- 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 安装

### 作叉状

1.  [叉回购](https://github.com/scottmathson/simpol-theme#fork-destination-box)
2.  用`$ git clone git@github.com:username/reponame.git`克隆回购
3.  删除`simpol-theme-screenshot.jpg`文件和示例帖子
4.  用`$ gem install bundler`安装捆扎机
5.  用`$ bundle install`安装宝石
6.  用`$ bundle exec jekyll serve`运行 Jekyll
7.  玩得开心！

### 作为哲基尔的主题宝石

[![Simpol Jekyll Theme - Gem version](img/08f271f86b7e84b621fdbb333b2b31b5.png)T2】](https://badge.fury.io/rb/simpol-jekyll-theme)

只需运行`$ gem install simpol-jekyll-theme`即可下载最新的 Gem

或者:

1.  下载主题，[快速下载链接](https://minhaskamal.github.io/DownGit/#/home?url=https://github.com/scottmathson/simpol-theme/tree/master/)
2.  用`$ gem install bundler`安装捆扎机
3.  用`$ bundle install`安装宝石
4.  用`$ bundle exec jekyll serve`运行 Jekyll
5.  玩得开心！

### 用 Docker 运行

*   Docker 安装指南可以在这里找到:[https://docs.docker.com/engine/installation](https://docs.docker.com/engine/installation)

您可以通过浏览到项目目录并运行`docker-compose up`在本地启动站点。

您应该会看到以下内容:

```
simpol-theme | Configuration file: /srv/jekyll/_config.yml
simpol-theme | Source: /srv/jekyll
simpol-theme | Destination: /srv/jekyll/_site
simpol-theme | Incremental build: enabled
simpol-theme | Generating...
simpol-theme | done in x.xxx seconds.
simpol-theme | Auto-regeneration: enabled for '/srv/jekyll'
simpol-theme | Server address: http://0.0.0.0:4000/
simpol-theme | Server running... press ctrl-c to stop. 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 当前特征

*   极简的设计和感觉。
*   简单且可定制的导航、索引和页脚
*   *网站问候语、子问候语、链接、徽标等*
*   所有这些都可以在配置文件中轻松设置和管理
*   干净，轻量级，页面和文章的默认布局
*   搜索引擎友好！针对 SEO 的优化元数据。
*   简单、全网站的谷歌分析设置/集成
*   文章类别和存档页面
*   所有帖子的 Twitter、脸书和电子邮件社交分享。
*   站点地图、XML 提要和 404 页面
*   单一的、功能强大的轻量级 CSS 样式表
*   **简单，像一个网站应该的样子。**

请通过[推文发送功能请求，回复到此状态](https://twitter.com/intent/tweet?in_reply_to=856908965778604032)。

## 资源

查看这些博客帖子，帮助您开始使用 Simpol Theme 写博客。帖子里有更多的资源可以帮助你。

*   [降价概述](https://dev.to/blog/2017/04/15/markdown-overview-blogging-with-jekyll)
*   [添加新帖子，简单博客](https://simpoltheme.com/tips/tricks/2017/04/16/writing-new-post/)
*   [将 Jekyll 部署到 GitHub 页面](https://jekyllrb.com/docs/github-pages/)

Simpol 的目的是让您通过 GitHub 页面使用免费的托管和存储库部署选项！[在这里了解更多关于 Jekyll / GitHub 页面的信息](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/#step-4-build-your-local-jekyll-site)-Simpol 主题最简单的设置方法，**几分钟内即可启动并运行**。