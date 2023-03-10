# 在 CSS 响应设计中使用 calc()

> 原文：<https://dev.to/tyzia/using-calc-in-css-responsive-design-5l1>

# 如何在 css 样式中使用 calc()

上次我发了一个帖子，讲述了我在响应式设计、添加多个断点以及在 excel 中做大量数学运算方面的挣扎。帖子可以在这里找到。感谢[反余弦的](https://dev.to/arccosine)评论，让我了解了 CSS 中的 calc()函数。我在我的 css 文件中应用了它，它非常有效！

以下是 html 代码:

```

div id=A

    div id=B
        img
        img
        img
        img
        img
    /div

/div

```

在浏览器中，这看起来像这样:

[![div in browser](img/eb22b525ea3fd9e1241f010cdbb17645.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GmDxYSf0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/px7l76r0ae23noat0oj9.JPG)

长话短说:我有 5 张社交图标的图片，每张 51px，宽度固定。他们的容器(div id=B)的宽度是 480px。我想根据屏幕大小计算图标之间的边距。所以我的 css 计算是这样的:

```

#B img {
    margin-right: calc((100% - 255px) / 4);
}

#B img:last-child {
    margin-right:0
}

```

只是一个小小的解释:

1.  我们取图标容器宽度的 100%。是 480px。
2.  然后我们扣除所有图标的宽度，就是 5*51px = 255px。
3.  这给了我们容器中剩余的空间。我们需要把它除以 4。因为我们在 5 个图像之间有 4 个间隙。
4.  所以我们有我们的保证金的长度-对。我们将最后一个图标的边距设为零。

现在，如果我们调整屏幕的大小，容器的宽度将是 460 像素，右边距将根据新容器的宽度重新计算。

就这么短，这么简单。再次感谢反余弦。