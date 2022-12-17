# 添加搜索到您的钴网站-第一部分

> 原文：<https://dev.to/booyaa/adding-search-to-your-cobalt-site---part-one-30pl>

这将是一个两部分的帖子，在这里我详细描述了在我的[钴](https://github.com/cobalt-org/cobalt.rs)网站上启用搜索的步骤。

在第一篇文章中，我将详细介绍如何使用手动创建的文档集合来集成 [lunr](http://lunrjs.com/) 。如果你已经知道如何连接 lunr，你可以跳到[的第二篇文章](https://dev.to/2017/adding-search-to-your-cobalt-site-part-two/)，在那里我使用一个液体模板创建文档集合。

我喜欢博客，但是在最初发现的兴奋之后，你会对博客作者和他们是否有类似主题的其他帖子感到好奇。此时，您希望有一个本地搜索或某种形式的分类法。

这通常是使用静态站点生成器来驱动你的博客的缺点。它非常适合生成预先呈现的静态页面，但是搜索或标签视图属于动态内容管理系统的领域。

正如这篇博文的标题所暗示的，我将寻求解决本地搜索的问题。我将把分类法的主题留给以后的博客文章。

事实证明，我们可以使用 lunr 近似一个合理的搜索体验。lunr 是企业搜索平台(现成的搜索引擎)Solr 的轻量级实现。通过一点 jQuery 和 lunr 库，我们可以用 Cobalt 拼凑一个搜索页面。

## 我的钴源目录结构

我分享了我自己的博客结构，作为一种在 Cobalt source 目录中引用位置的方式。

```
.
├── _drafts
├── _layouts
├── blog
├── img
└── js 
```

Enter fullscreen mode Exit fullscreen mode

这里应该没有意外，我们感兴趣的主要领域是 javascript 相关资产所在的`js`目录。以及 my index、about 和 licenses 页面所在目录的根目录(所有这些都扩展了默认的 liquid 模板)。

## 搜索.液体

我把我的搜索模板放在我的钴源的根目录下，它看起来像这样。

### 正面物质

```
extends: default.liquid

title: search
path:  search/
-------- 
```

Enter fullscreen mode Exit fullscreen mode

### 内容

模板的一般要点(双关语)是创建一个文本输入框和一个显示结果的钩子。

该模板是这个[要点](https://gist.github.com/sebz/efddfc8fdcb6b480f567)的副本，用于让 lunr 与 [hugo](http://gohugo.io/) (一个用 go 编写的优秀静态站点生成器)一起工作。

它进行了一些调整，以使其与 lunr 2 . 1 . 0 版兼容

```
<input id="search" type="text" size="25" placeholder="search for stuff here..." 
       autofocus><br />

<ul id="results">
</ul>

<script type="text/javascript" 
        src="https://code.jquery.com/jquery-2.1.3.min.js"></script>
<script type="text/javascript" src="https://unpkg.com/lunr/lunr.js"></script>
<script type="text/javascript">
var lunrIndex,
    $results,
    pagesIndex;

// This is pretty much a copy of 
// https://gist.github.com/sebz/efddfc8fdcb6b480f567
// Initialize lunrjs using our generated index file
function initLunr() {
    // First retrieve the index file
    $.getJSON("/js/lunr_docs.json")
        .done(function(index) {
            pagesIndex = index;

            // Set up lunrjs by declaring the fields we use
            // Also provide their boost level for the ranking
            lunrIndex = lunr(function() {
                this.field("title", {
                    boost: 10
                });

                this.field("content");

                // ref is the result item identifier (I chose the page URL)
                this.ref("href");

                // Feed lunr with each file and let lunr actually index them
                pagesIndex.forEach(function(page) {
                    this.add(page)
                }, this);
            });

        })
        .fail(function(jqxhr, textStatus, error) {
            var err = textStatus + ", " + error;
            console.error("Error getting cobalt index file:", err);
        });
}

// Nothing crazy here, just hook up a listener on the input field
function initUI() {
    $results = $("#results");
    $("#search").keyup(function() {
        $results.empty();

        // Only trigger a search when 2 chars. at least have been provided
        var query = $(this).val();
        if (query.length < 2) {
            return;
        }

        var results = search(query);

        renderResults(results);
    });
}

/**
    * Trigger a search in lunr and transform the result
    *
    * @param  {String} query
    * @return {Array}  results
    */
function search(query) {
    // Find the item in our index corresponding to the lunr one to have more 
    // info
    // Lunr result: 
    //  {ref: "/section/page1", score: 0.2725657778206127}
    // Our result:
    //  {title:"Page1", href:"/section/page1", ...}
    return lunrIndex.search(query).map(function(result) {
            return pagesIndex.filter(function(page) {
                return page.href === result.ref;
            })[0];
        });
}

/**
    * Display the 10 first results
    *
    * @param  {Array} results to display
    */
function renderResults(results) {
    if (!results.length) {
        return;
    }

    // Only show the ten first results
    results.slice(0, 10).forEach(function(result) {
        var $result = $("<li style=\"list-style:none;\">"); // FUUUUUU!
        $result.append($("<a>", {
            href: result.href,
            text: "» " + result.title
        }));
        $results.append($result);
    });
}

// Let's get started
initLunr();

$(document).ready(function() {
    initUI();
});
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## 手工制作的 lunr 文档集合

为了让我的概念证明继续下去，我需要给 lunr 一个我的博客帖子的精华形式，我把它叫做`lunr_docs.json`并存储在`/js`中。在这篇文章的早期版本中，我错误地将其称为 lunr 索引。

这些数据将用于生成 lunr 的索引。索引有不同的结构，我们将在第二部分的[中了解。](https://dev.to/adding-search-to-your-cobalt-site-part-two) 

```
[{  "title":  "Useful commit messages",  "href":  "/2017/useful-commit-messages/",  "content":  " Keeping a copy of this excellent bit of advice until I've committed (no pun) it to memory. "  },  {  "title":  "Add reading time in Cobalt",  "href":  "/2017/add-reading-time/",  "content":  " I wanted to add an approximate reading time to each of my blog posts, like those seen in medium posts. "  },  {  "title":  "Using a custom domain with GitHub Pages",  "href":  "/2017/gh-pages-custom-domain/",  "content":  " It took far too long to work out how to do this on the GitHub help pages... "  },  {  "title":  "Using Cobalt with GitHub pages",  "href":  "/2017/cobalt-github/",  "content":  " It turns out using Cobalt and your personal GitHub page is a bit trickier to setup. Your personal GitHub page as oppose to your repo GitHub page, must have the content in the master branch. Repository/Project GitHub pages can live in a subdir of default branch i.e. docs "  },  {  "title":  "MacBook Air Setup",  "href":  "/2017/mba-setup/",  "content":  " Here's my current setup for my MacBook Air Setup. I use a range of tools like homebrew, Visual Studio Code and vim. "  }] 
```

Enter fullscreen mode Exit fullscreen mode

文档集的格式相当简单，只有很小的博客文章片段，这将影响搜索。

顺便提一下，搜索模板中的`title`字段的`boost`属性可能是超级错误的，因为它是唯一一个被再次搜索的项目。代码的原始源代码也利用了一个`tag`字段，并且`boost`允许你在搜索索引时给哪个字段一个权重。

正如你所想象的，手工制作一个文档集有点低保真度，所以如果你想看看我最后用了什么(另一个液体模板)，看看这篇博文的第二部分。

## 把所有的东西放在一起

一旦您创建了搜索模板和 lunr 索引，您需要做的就是执行您通常的`cobalt build`工作流程。

如果您关注了我的结构，您的搜索页面可以在`/search`中找到。当您开始在输入框中键入内容时，搜索结果应该会立即出现。

## 但它是 webscale 吗？

我不知道，我没有足够大的数据量来测试。然而，lunr v2.x 的`lunr.Index.load`函数允许您加载一个预构建的索引，但这确实增加了额外的复杂性。并且在编写时需要 node.js 或某种形式的 v8 上下文来生成它。这个想法是，您对文档集合进行索引，并对生成的索引进行序列化。

更多细节可以在这里找到[。](https://lunrjs.com/guides/index_prebuilding.html)

## 还有别的吗？

我很想消除对 jQuery 的依赖，但公平地说，我不会笨到用普通的 js 重写，因为它正好可以工作。说到这里，这让我想到，也许我应该创建一个 liquid-rust 扩展(不确定它是一个标签、过滤器还是其他),来生成普通的 js，以满足人们对 jQuery 的需求。

我也不知道为什么该死的输入框这么小。

对于这两个问题的任何指导或帮助都将不胜感激。

## 更新

2017-06-21 -将对 lunr 索引的引用更改为 lunr 文档集合。