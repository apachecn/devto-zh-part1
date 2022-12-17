# 从 CMS 切换到 Jekyll

> 原文：<https://dev.to/ajaykarwal/switching-from-a-cms-to-jekyll>

早在二月份，我发布了我网站的新版本。只有一个非常轻微的设计更新，但它是如何建立一个完整的大修。

> ![](img/aceed3bd213574fbcb66f79882787f50.png)Ajay Karwal@ ajaykarwal![](img/4d9c44713c216584b3d48ff3455cbb68.png)“新”版我的网站现已上线。[ajaykarwal.com](https://t.co/3lnJDCSY9S)
> 内容相同(ish)但其已使用[@ jekylrb](https://twitter.com/jekyllrb)&[@ travisci](https://twitter.com/travisci)22:52PM-09 2017 年 2 月[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=829825398385082368)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=829825398385082368)0[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=829825398385082368)4

所有的东西最初都是用 [Umbraco](https://umbraco.com/) 制造的——一个 <abbr title="Content Management System">CMS</abbr> 由。并且托管在微软 Azure 上。这种设置当然依赖于 Windows 进行开发。

我是一名 Mac 用户，所以为了更新我的网站，我需要在 VMmare Fusion 中运行 Windows，在 Visual Studio 中启动项目，设置我的 localhost 和 IIS Express，登录到 Umbraco 仪表板，然后进行更新。

当涉及到部署我的更改时，我必须 FTP 我的视图、文档类型(模板)、数据类型、dll 和静态资产。

现在就杀了我吧。😭

## 拾级而上的哲基尔

我第一次听说[杰基尔](https://jekyllrb.com/)和其他 <abbr title="Static Site Generator">SSG</abbr> 是在[工具日播客](http://www.toolsday.io/episodes/static-site-gens.html)上(正好在我发布网站更新的一年前)，从那以后它就一直在我的*“我想尝试一天的事情”*清单上。

我首先创建了一个空白的 Jekyll 站点。

```
jekyll new myblog --blank 
```

如果你遵循[快速入门指南](https://jekyllrb.com/docs/quickstart/)，你会得到一个简单的博客主题，这是我不想要的，因为我要导入我现有的设计。

这个过程的问题是命令`bundle exec jekyll serve`不起作用，因为安装没有`Gemfile`或`_config.yml`文件。稍后将详细介绍。

安装完成后，Jekyll 会创建组织你的网站所需的基本文件夹结构。

```
├── _drafts
├── _layouts
├── _posts
└── index.html 
```

请注意文件夹是如何以下划线开头的。任何以这种方式命名的文件夹都不会输出到编译后的`_site`文件夹中。

## 迁移我的内容

我开始把我在 Umbraco 项目中的所有观点复制到我的 Jekyll `_layouts`文件夹中。幸运的是，两个系统都使用了相似的布局模板、页面模板和局部视图概念(Jekyll 中的`_includes`)，所以要把我的文件结构做好相对来说没什么困难。

接下来我把我的视图逻辑从 C#/Razor 转换成了 [Liquid](https://shopify.github.io/liquid/) ，Jekyll 使用的模板引擎(由 Shopify 开发)。所有的逻辑都有类似的替换，因为我的网站没有做任何太复杂的事情。

Jekyll 内置了 sass 预处理，所以只需复制我的 Sass 文件夹并添加一个下划线，就可以在构建时忽略它。我继续添加我的剩余资产，包括和模板。你可以在我的 [GitHub 库](https://github.com/ajaykarwal/portfolio)上看到完整的站点结构。

## 魔法发生的地方

您需要创建的最重要的文件是项目根目录中的一个`Gemfile`和一个`_config.yml`文件。

`Gemfile`列出了构建项目所需的任何 Ruby Gems。这个文件至少应该包含，

```
source 'https://rubygems.org'
gem 'jekyll' 
```

文件是奇迹发生的地方。它包含所有的网站设置，你可以添加任何自定义设置，然后使用`{% raw %}{{ site.SETTING_NAME }}{%` `endraw` `%}`将这些设置应用到你的液体模板中。你可以使用[我的配置文件](https://github.com/ajaykarwal/portfolio/blob/master/_config.yml)作为你自己项目的模板，或者遵循 [Jekyll 文档](https://jekyllrb.com/docs/configuration/)。

## 构建你的静态站点

既然基本的文件和文件夹结构已经就绪，只需运行，

```
bundle exec jekyll serve --watch 
```

这将捆绑在你的`Gemfile`中定义的任何 Ruby Gems，生成你的静态文件到一个`_site`文件夹，在`localhost:4000`上提供你的站点，并且从现在开始观察你所做的任何改变。

就这样，你完了。

祝贺您将 CMS 驱动的网站转换为由 Jekyll 驱动的静态网站。🎉

## 结论

总的来说，从 Umbraco 到 Jekyll 的过程相对简单。文档非常清楚，对于在液体模板中需要定制逻辑的场景，有很多关于栈溢出的支持。

每种使用情形都会有所不同，所以不要全信，但我认为适用于所有情形的主要优点和缺点是:

### 优点

*   输出只是静态的 HTML、CSS 和 JavaScript，因此最终的网站加载速度极快。它还可以利用浏览器缓存使回访更加迅速。
*   不需要复杂的主机包或服务器端处理，这可以大大降低运行网站的成本。你甚至可以通过 [GitHub 页面](https://help.github.com/articles/using-jekyll-as-a-static-site-generator-with-github-pages/)免费托管它。
*   用 Markdown 编写内容会产生干净的语义标记，并鼓励对内容采用更结构化的方法。

### 弊

*   每次进行编辑时，都需要使用命令行来生成您的站点，因此需要一些开发专业知识。这意味着对于内容由客户自己管理的客户项目来说，Jekyll 可能不是一个好的解决方案。
*   不太适合更新非常频繁的网站。

## 最后的想法

我强烈建议你试试《化身博士》。建立一个测试项目，感受一下内容的结构。Liquid templating engine 是一个很好的工具，入门门槛非常低。

如果你在你的网站上使用 Jekyll，我很想听听你的经历。