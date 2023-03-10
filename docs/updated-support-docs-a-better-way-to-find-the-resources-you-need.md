# 更新的支持文档:找到所需资源的更好方式

> 原文：<https://dev.to/sparkpost/updated-support-docs-a-better-way-to-find-the-resources-you-need>

## 支撑支撑

也许你是 SparkPost 的新手，你需要一个指南来帮助你开始使用。或者，也许你是一个长期的专业人士，你想测试你的[生产列表与水槽服务器](https://www.sparkpost.com/docs/faq/using-sink-server/)。对于这些问题以及介于两者之间的任何问题，您都可以在我们的[支持文档](https://www.sparkpost.com/docs/)中找到答案。

对大多数公司来说，这听起来很标准，对吗？不幸的是，我们从用户那里得到了很多反馈，说我们的支持网站很难使用。从他们的角度来看，搜索并不总是返回最好的结果，导航有时会令人困惑，内容样式不一致，设计隐藏了折叠下页面的信息线索。我们在幕后使用 Desk.com 来构建网站，这是一个很好的解决方案，但不允许我们支持团队以外的人，包括我们优秀的社区成员，来改进我们的文档。

我们的新设置不仅允许我们的用户快速找到他们需要的内容，还允许他们提交修改，甚至添加新文章。

## 什么变了？

TL；我们把支持文档转移到 GitHub，开源它，把文章推到 Algolia 进行搜索，并且写了一个管道直接进入我们的 sparkpost.com WordPress 网站。咻！我们还重新设计了登录页面，以帮助调出入门资源、从其他提供商处迁移以及获取帮助。让我们卷起袖子，深入探讨我们所做的一些改变。

## 化为杂草

### 越狱:黑客风格

第一步是把我们的文章带出 Desk.com。他们有一个 API，所以我们写了一个[脚本](https://github.com/SparkPost/support-docs/tree/master/export)从 desk 导出文章，并将它们从 HTML 转换成 Markdown。这相对容易，尽管有些乏味——因为 Markdown 转换器在处理混乱的 HTML 时遇到了一些麻烦，需要一些手工清理。

[![oh no](img/c36a57c9ea466ef37030631dbdcc59d6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OAJpJwfd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media2.giphy.com/media/3KQ4VNwCrOThC/giphy.gif)

每一类文章都存储在一个文件夹中，该文件夹有一个 index.md 文件来存储任何元数据。每个类别文件夹中都有一个媒体文件夹，用于存放所有相关图像。

```
articles/
├── category_name/
│   ├── media/
│   │    └──my_article/
│   │       └──some_picture.png
│   ├── index.md
│   ├── my_article.md
│   └── another_article.md 
```

Enter fullscreen mode Exit fullscreen mode

### 按字

接下来是将文章推送到 WordPress。首先，我们需要在 WordPress 中为支持文章和类别设置一个定制的文章类型和分类。我们使用由我们自己的[杰森·罗德斯](https://twitter.com/rhodesjason)编写的[库](https://github.com/jasonrhodes/WP-CustomObjects)来简化我们自定义帖子类型的管理。有了几个定制模板，我们在这方面做得很好。

```
<?php
// Support Article CPT
new CustomObject('support_article', array(
  'singular_name' => 'Support Article',
  'plural_name' => 'Support Articles',
  'description' => 'Support Articles',
  'supports' => array('title', 'editor', 'author', 'excerpt', 'custom-fields', 'revisions'),
  'public' => true,
  'has_archive' => false,
  'show_ui' => false,
  'taxonomies' => array('support_category'),
  'rewrite' => array(
    'slug' => 'docs/%support_category%',
    'with_front' => false,
  ),
));

// Support Category CT
register_taxonomy('support_category', 'support_article', array(
  'labels' => $support_category_labels,
  'rewrite' => array(
    'slug' => 'docs',
    'with_front' => false,
    'hierarchical' => true,
  ),
)); 
```

Enter fullscreen mode Exit fullscreen mode

关于搜索，我们决定使用 [Algolia](https://www.algolia.com/) ，它和我们的 [API 文档](https://developers.sparkpost.com/api/?_ga=2.165630079.1914068973.1496187918-424867781.1485888065)配合得非常好。他们构建了一个非常棒的 WordPress 插件，可以处理索引、多种环境和你看到的实时搜索。不用说，我们是你的超级粉丝。

### 铺设管道

这最后一块是最复杂的。我们需要以一种跨本地、暂存和生产环境的方式推动对类别、文章和媒体的任何更改。

为了实现这一点，我们使用了:

*   Bash + [WP CLI](http://wp-cli.org/) ，一个从命令行与 WordPress 交互的超级有用的工具。
*   一些把 Markdown 转换成 HTML 的 JS。
*   Travis CI 用于自动化构建和部署

我们围绕 WP CLI 编写了一个包装器来处理存储在 Travis 中的环境变量。在每次部署期间，我们首先[更新类别](https://github.com/SparkPost/support-docs/blob/master/bin/deploy-categories.sh)，然后[更新每篇文章](https://github.com/SparkPost/support-docs/blob/master/bin/deploy-articles.sh)。类别相当简单:找到改变的 index.md 文件，并在 WordPress 中更新相应的类别。

处理文章是有趣的地方。有很多州需要考虑。您可以替换和移动文章中的图像，将文章移动到不同的类别，甚至删除整个文章并重新运行部署。我们采取了最安全的方式，在每次更新时围绕文章重建整个世界，包括元数据、图像和实际内容。

不要让这个欺骗了你——虽然这听起来相对简单，但是上面的每一个步骤都可能是一篇完整的博文。也许有一天我会深入研究这个问题，但是现在，请随意研究一下[部署脚本](https://github.com/SparkPost/support-docs/tree/master/bin)，如果您有任何问题，请联系我们。如果你在我们的文档中发现了一个错别字，或者想写一篇关于用你最喜欢的电子邮件客户端/工具/语言使用 SparkPost 的文章，只需在 [docs repo](https://github.com/SparkPost/support-docs) 上提交一份 PR，我们就会看一看！

这篇文章最初发表在 [SparkPost 博客](https://www.sparkpost.com/blog/updated-support-docs/)上。