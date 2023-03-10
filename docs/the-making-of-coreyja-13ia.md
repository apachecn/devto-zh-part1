# corneja 的制作

> 原文：<https://dev.to/coreyja/the-making-of-coreyja-13ia>

**原帖[coreyja.com](https://coreyja.com/making-of-coreyja/)2017-02-18**

嘿，谢谢你来看我的博客！一切都准备好了，所以我决定我的第一篇文章应该是展示我是如何把这个博客整合在一起的！如果你想直接跳到源代码，这里是 github:[github.com/coreyja/coreyja-blog](https://github.com/coreyja/coreyja-blog)。

## 要求

有些事情我希望我选择的引擎能够支持。

*   必须支持用 Markdown 写的文章
    *   我真的很喜欢用 Markdown 写东西，它可以转化成 HTML
*   语法突出显示
    *   Markdown 支持代码块，我希望它们被正确地高亮显示
*   生成静态站点
    *   我过去运行过 Wordpress 博客，但是我不想维护一个像那样动态生成的博客
*   用 Ruby 编写，并使用 ERB 诱人的引擎
    *   这只是个人喜好，但是我喜欢能够扩展我的工具，我目前正在用 Ruby 工作，并且喜欢 ERB 语法

## 中间人博客

带着这些要求我找到了`middleman`([github.com/middleman/middleman](https://github.com/middleman/middleman))宝石！以及它的一些延伸，包括`middleman-blog`([github.com/middleman/middleman-blog](https://github.com/middleman/middleman-blog))，以及`middleman-syntax`([github.com/middleman/middleman-syntax](https://github.com/middleman/middleman-syntax))。

中间人将自己描述为一个静态站点生成器，这正是我要找的。我来自 Rails 背景，在中间人环境中如鱼得水。开始就像运行博客生成器和编辑一些 ERB 文件一样简单。

中间人还提供了一个机架兼容的`config.ru`文件，这使得使用 [POW](http://pow.cx/) 进行开发变得轻而易举。只需将项目链接到 POW，就可以在本地查看站点，并在我重新加载时显示更改。我没有启用实时重新加载，但也有一个扩展。

## 设计

这个网站的设计是由菲利普·英奇在 phillipthedesigner.com/完成的。几年前我们作为自由职业者一起工作，当我们一起工作时，我让他为我设计了这个博客。我认为它绝对经受住了时间的考验，几年后仍然看起来很棒！我最初使用的是我当时为 django 设计的作品集网站，但是我打算用这个博客来代替它。这一次，我对设计做了一些小的调整，使它们反应灵敏。

## 语法高亮显示

语法突出显示是我对这个项目的要求之一。用中间人语法设置它相当简单，但是我想更进一步！我意识到他们在幕后使用了[流氓](https://github.com/jneen/rouge)，主题可以在 Ruby 中定义。所以我做了一个语法突出主题，适合这个网站的设计和配色方案。在很大程度上，我是以`Monokai`主题为基础的，但是改变了强调色以更好地适应我的配色方案。我对结果非常满意！我不想在我的日常编辑中使用它，但我认为它在网站上看起来很不错。

## 托管

由于中间人是一个静态网站生成器主机更容易和更便宜！我只是将生成的文件上传到 AWS S3，并为静态站点配置它！可以理解的是，这比我同时经营的一些 Heroku 个人项目要便宜得多。