# 如何在 Jekyll 的帖子中添加作者简介

> 原文：<https://dev.to/m0nica/how-to-add-author-bio-to-posts-in-jekyll-3g1>

我的博客 [Datalogues](https://datalogues.com) 由 [Jekyll](https://jekyllrb.com/) 驱动，一个静态站点生成器。我为网站选择的主题不支持现成的作者，但是在 Jekyll 中实现作者功能是很容易的。

### 1)在 Jekyll 项目中编辑/创建适当的文件夹和文件

*   应该包含作者的个人博客帖子的正面内容
*   _layouts/post.html

并创建了以下文件夹/文件:

*   _data/authors.yml
*   _includes/author_bio.html

### 2)存储作者数据

我将我的作者数据存储在一个名为`_data`的文件夹中，其中包含一个文件`authors.yml`。与`monica_powell`相关联的作者信息从`authors.yml`数据文件中提取到我的帖子中。

```
monica_powell:
    name: Monica Powell
    email: monica@aboutmonica.com
    twitter: http://twitter.com/waterproofheart
    bio: Monica Powell is a web technologist that cares about increasing the visiblity of underestimated individuals in technology. In 2015, she received the &#35;GIRLBOSS award from Sophia Amoruso’s Girl Boss Foundation. She’s currently focusing on making tech more enjoyable & accessible and is always up to chat data visualizations, web development or &#35;BlackGirlMagic.
    image: http://www.datalogues.com/asseimg/monica-powell-headshot.jpg 
```

Enter fullscreen mode Exit fullscreen mode

### 3)在个人博文的前面引用相关作者

在 Jekyll 的每篇博文的前面，你应该使用下面的格式`author: NAME OF AUTHOR`引用[【YAML】(YAML 不是标记语言)](http://www.yaml.org/start.html)的作者。作者的名字应该与`authors.yml`中的一个变量完全匹配

Jekyll 中的 front matter 设置了帖子的元数据，是正确构建帖子的关键。YAML 是所有编程语言的人性化数据序列化标准。

这里有一个例子，为这个特殊的职位的前沿问题。

```
layout: post
title: How to Add Author Bio in Jekyll
description: A guide to adding author bios in Jekyll
image: asseimg/author-bio.png
permalink: adding-author-bios-in-jekyll
author: monica_powell
comments: true 
```

Enter fullscreen mode Exit fullscreen mode

### 4)为作者简介定义 HTML

在文件夹`_includes`中创建一个名为`author_bio.html`的文件来定义作者简介应该如何显示的 HTML