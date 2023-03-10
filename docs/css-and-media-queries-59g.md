# CSS 和媒体查询

> 原文：<https://dev.to/tyzia/css-and-media-queries-59g>

# 向响应 CSS 添加额外断点的策略

我是学 CSS 的，所以我相信那些有经验的前端开发人员会对这个帖子会心一笑。几年后我也会。尽管如此，我会试着写下我的发现，只是为了更好地记住它。

我有这段代码:

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

我试着让这些图标在页面上居中，左右两边有固定的间距。和图标之间相等的间距。

在我的 CSS 中我有这样的:

```

#A {
    width: 56%;
    margin: 0 auto;
}

```

图标宽度相同，为 51px。为了让它们看起来放置得更好，我为图像设置了 padding-right，为最后一张图像设置了 padding-right:0。任务是从 768 像素的屏幕宽度开始，使这个页面具有响应性。

我最终得到了一个计算表，就像这样:

```

A = current width of screen/container, div id=A
B = div wrapper for icons = 0.56*A, div id=B
I = icon image width = 51
P = padding-right = (B-5*I)/4
% = (P/B)*100 rounded down
rounded means that if I had 3.99%, it would be rounded down to 3.

```

下一步是看看，在哪个断点我需要改变填充百分比。因为如果我设置'右填充:10% '的话，它只适用于 768 像素和 758 像素之间的屏幕宽度。

实际上，这可以用数学方法来检验:

假设我们的屏幕宽度为 768 像素。在这种情况下，我们有:

a = 768
B = 0.56 * 768 = 430.08
I = 51
P =(430.08-5 * 51)/4 = 43.77
% =(43.77/430.08)/100 = 10.1771
下舍入(10.1771) = 10

所以我们已经证明，对于 768px 的屏幕宽度，填充应为 10%。

现在，让我们假设我们将填充设置为 10%(容器宽度)，并将屏幕尺寸调整为 757 像素。最后一个图标图像将跳转到下一行，如下所示:

[![icons jumped to next line](img/0042217efcbd8fdca8c1dbb21f63301f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HLT2SKC6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jy44yfmval6jg4rd1rc7.JPG)

为什么会这样？看起来一切都是正确的，我们使用的是百分比，它不应该超出容器的限制，图像不应该跳到下面。实践表明，这种假设是错误的。

所以我决定用@media query 在我的 CSS 中添加断点。我对可能的断点的计算是:

我创建了 excel 文档，插入了我的计算公式(见上文),并将这些公式应用于屏幕尺寸的每一个可能值，例如 768 像素、767 像素、766 像素...475px。

我注意到，从 768px 到 759px，图像之间的填充值是容器的 10%(div id = B，见上文)。但是 758px 的屏幕尺寸，填充变成了 9%。在屏幕尺寸达到 712 像素之前一直如此。在屏幕尺寸为 711 像素时，填充为 8%，依此类推。有了这种至高无上的价值观，我找到了所有的细分市场:

宽度介于 758px 和 768px 之间，填充 10%
宽度介于 711px 和 758px 之间，填充 9%
宽度介于 669px 和 711px 之间，填充 8%
宽度介于 632px 和 669px 之间，填充 7%
宽度介于 599px 和 632px 之间，填充 6%
宽度介于 569px 和 599px 之间，填充 5%
宽度介于 542px 和 562 px 之间

如果宽度低于 475 像素，填充为 0%。这表明，我应该缩小父容器的左右填充，因为当屏幕小于 475px 时，5 个图标无法放入它们的 div id=B。

基于此，我将媒体查询添加到我的 CSS 中，如下所示:

```

@media all and (min-width:758px) and (max-width:768px) {
    #social img {
        padding-right: 10%;
    }
}

@media all and (min-width:711px) and (max-width:758px) {
    #social img {
        padding-right: 9%;
    }
}

...

@media all and (min-width:475px) and (max-width:494px) {
    #social img {
        padding-right: 1%;
    }
}

```

我唯一担心的是:如果屏幕大小正好在断点边缘，比如 758 像素，会发生什么？

填充是 10%(因为最小宽度= = 758 像素且为真)还是 9%(因为最大宽度= = 758 像素且为真)？

在这种情况下，css 的级联规则——CSS 文件中后面的指令将覆盖它上面的相同指令——回答了我的问题。因为指令“最大宽度:758px”在指令“最小宽度:758px”之后，所以填充为 9%。

谢谢你看了这个无聊的帖子。我想，有更好的方法让你的页面响应，而不是创建 10 个媒体查询并花费整个晚上计算分段。))小心！