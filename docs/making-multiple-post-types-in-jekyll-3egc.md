# 在 Jekyll 中创建多种帖子类型

> 原文：<https://dev.to/chrisrhymes/making-multiple-post-types-in-jekyll-3egc>

最近，我决定在我的网站上开辟一个新的短篇小说板块。这让我进退两难，不知道如何最好地使用 Jekyll 和 GitHub 页面创建多种帖子类型。我发现过去人们有几种不同的方式，但我发现收藏是最好的方式，所以我想分享一下我是如何做到的。

## 收藏目录

首先在您的项目中创建一个新目录，其名称以下划线开头。我把我收集的故事命名为我创建了一个名为`_stories`的目录。

## 配置

接下来，用一个新的 collections 部分更新您的 _config.yml 文件，并在该部分下添加您的集合名称，这次没有下划线，在我的示例中是`stories`。然后，假设您想将您的每个故事作为一个单独的页面输出，添加`output: true`。

下面显示了添加的内容。

```
collections:
  stories:
    output: true 
```

Enter fullscreen mode Exit fullscreen mode

## 创建页面和布局

现在您可以开始在您的收藏目录(`_stories`)中创建页面。它看起来像 html 或 markdown 格式的作品，但我创建了我的 markdown，所以我可以利用模板。

我为故事创建了一个新的模板，因为我希望它们与标准的博客文章页面有所不同。我在`_layouts`目录中创建了一个`story.html`文件。这个模板可以像你想的那样简单，因为它真正需要的只是`{{ title }}`和`{{ content }}`占位符。我在布局中给 story.html 添加了一些前置的东西，所以它继承了默认的布局，但是你可以编辑它来适应你的站点布局。

下面是一个简单的收藏页面布局的例子。

```
--------
layout: default
--------

<h1>{{ title }}</h1>
{{ content }} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们进入您的收藏内容页面。你所需要做的就是为每个内容页创建一个单独的降价页面，包括以下内容、布局、标题和描述。

```
--------
layout: story
title: Story Title Goes Here
description: "The story description text goes here"
-------- 
```

Enter fullscreen mode Exit fullscreen mode

然后把你的内容写在 markdown 下面，就像你写帖子一样。

## 创建索引页面

最后，你可能需要一种方法来访问你的收藏页面。我在站点根目录下创建了一个`stories.html`页面，作为故事的索引页面。如果你愿意，你可以创建一个名为`stories`的目录，放一个 index.html 页面在里面。

文档上说你可以在`_stories`目录中创建一个页面，并给它一个永久链接，但对我来说，这也让它出现在下面的循环中，所以它链接到了自己。

无论如何，您可以在您的`stories.html`页面中使用简单的 for 循环遍历集合中的页面，如下所示。

```
{% for item in site.stories %}
  <h2>{{ item.title }}</h2>
  <p>{{ item.description }}</p>
  <p><a href="{{ item.url }}">{{ item.title }}</a></p>
{% endfor %} 
```

Enter fullscreen mode Exit fullscreen mode

希望这是您开始使用 Jekyll 和 GitHub 页面创建多种帖子类型所需的全部信息。我的网站都设置好了。现在我需要做的是开始写一些[故事](https://dev.to/stories/) …