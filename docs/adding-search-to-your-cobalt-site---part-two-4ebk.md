# 添加搜索到您的钴网站-第二部分

> 原文：<https://dev.to/booyaa/adding-search-to-your-cobalt-site---part-two-4ebk>

这将是一个两部分的帖子，在这里我详细描述了在我的[钴](https://github.com/cobalt-org/cobalt.rs)网站上启用搜索的步骤。

您可能已经在第一部分中了解到，创建手动文档集合有点麻烦，使用 liquid templating engine 可以轻松完成。

[![booyaa image](img/0856925e87024eeeb56184e23bf8ed45.png)](/booyaa) [## 添加搜索到您的钴网站-第一部分

### mark Sta Ana Aug 19 ' 186min read

#cobalt #blogging #lunr](/booyaa/adding-search-to-your-cobalt-site---part-one-30pl)

## 肺液

### 身前要紧

```
title: lunr index
path: /js/lunr_docs.json
-------- 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意的是，Cobalt 将使用这个路径来创建 lunr 文档集合。

### 内容

```
[
{%  assign  idx  =  0  %}
{%  assign  post_count  =  posts  |  size  %}
    }{%  if  idx  <  post_count  %},{%  endif  %}
        "content" : "{{  post.content  |  strip_html  |  strip_newlines  
  |  replace:  "\",  "\\"  }}"
        "href" : "{{  post.permalink  }}",
        "title" : "{{  post.title  }}",
    { 
]
{%  endfor  %} 
```

Enter fullscreen mode Exit fullscreen mode

博客索引之间唯一真正的区别是，我使用索引来跟踪最后一篇文章，所以我可以省略后面的逗号。

不要忘记从 javascript 文件(`js/`)的资产目录中删除手动生成的文档集合(`lunr_docs.json`)，因为这让我感到困惑，并让我想知道为什么索引没有被更新。

## 更新

2017-06-21 -将对 lunr 索引的引用更改为 lunr 文档集合。