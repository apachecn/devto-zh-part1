# 如何样式页脚语义正确？

> 原文：<https://dev.to/tyzia/how-to-style-footer-semantically-correct-7kh>

我关心的是，我应该如何设计我的页脚，使其包含在我的页面的语义轮廓中。

我们假设，我的页面是这样的(不知道怎么贴 html 标签，所以去掉了''):

```
body

    header.../header
    main.../main

    footer
        h2 Main footer of the page /h2
        section
          h3 About this site /h3
        /section
        section
          h3 Contact form /h3
        /section
        nav
          h3 Footer navigation /h3
        /nav
    /footer

/body

```

如果我列出这段代码([这里](https://gsnedders.html5.org/outliner/))，我会看到:

```

1\. Header
2\. Main content of the page
2.1\. Main footer of the page
2.2\. About this site
2.3\. Contact form
2.4\. Footer navigation

```

这不是我想要的。在上面的大纲中，“页脚”是“主”节的一个子节，页脚的子节也是与“页脚”在同一级别的“主”节的子节。

我想有'页脚'在同一水平的'主要'部分和页脚的子部分作为其子部分。在大纲中看到这样的内容:

```

1\. Header
2\. Main content of the page
3\. Main footer of the page
3.1\. About this site
3.2\. Contact form
3.3\. Footer navigation

```

然后我读到，“‘页眉’和‘页脚’并不像‘节’一样划分内容，相反，它们的存在是为了在语义上标记一个节的各个部分。”。这给了我一个提示，我的“footer”标签中的所有内容在大纲方面都不是一个单独的部分。所以，我把我的“页脚”包装成“节”，这样做了，我的大纲也改进了:

```
body

    header.../header
    main.../main

    section
        footer
            h2 Main footer of the page /h2
            section
              h3 About this site /h3
            /section
            section
              h3 Contact form /h3
            /section
            nav
              h3 Footer navigation /h3
            /nav
          /footer
    /section

/body

```

这导致了这个纲要:

```

1\. Header
2\. Main content of the page
3\. Main footer of the page
3.1\. About this site
3.2\. Contact form
3.3\. Footer navigation

```

我的问题还是一样:这种方法正确吗——把‘页脚’包装成无意义的‘节’？请分享你的看法或经验。