# CSS 的盒子模型快速介绍

> 原文：<https://dev.to/promisetochi/quick-intro-to-csss-box-model-57>

这是一个关于你需要知道的盒子模型的快速介绍，什么应该避免，什么最好用。

CSS 盒子模型非常简单。每个 HTML 元素都被表示为一个矩形框，占据用户屏幕的不同部分。如下面的`div`元素所占据的空间，例如

```
<div>A div that will take up some rectangular space on the screen</div> 
```

Enter fullscreen mode Exit fullscreen mode

[![A screenshot of the div](img/3a70598d2e4ce0b2c5571799055099e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1X5s1Txj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0kwmqp0hwgsx8ibcqc5p.png)

上面的`div`标签是一个块元素，占据了其内容的高度(在本例中是文本)和其父元素的整个宽度。在这种情况下，其父元素是占据其父元素`html`全部宽度的`body`。在任何 HTML 文档中，`html`元素都是根元素，所以它自动占据 100%的屏幕宽度，所以作为其后代的每个 BLOCK 元素也占据 100%的屏幕宽度，除非它的宽度是用 CSS 显式设置的。

回到盒子模型，任何 HTML 元素的盒子都由四样东西组成，

1.  宽度和高度
2.  填料
3.  边境
4.  边缘

[![Box representing the css box-model](img/be921d6a6d6ee337f5018e6555e046fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aG0oWkFI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mt8rd6vs99689tfifnvn.png)

在 CSS 中，有两种方法可以计算一个元素的盒子模型，它们是，

1.  内容盒
2.  边框

内容框是浏览器默认使用的模型，也是最不理想的模型。
—
内容框模型的工作方式是，你指定一个元素的宽度和高度，对任何元素的框的任何进一步添加将被添加到它的宽度和高度。例如，

```
div {
  box-sizing: content-box;
  width: 200px;
  padding: 50px;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经指定了`div`元素占用`200px`的宽度，但是对于内容框模型，`div`将占用`300px`的宽度。也就是说，通过填充或边框添加的任何内容都将增加元素的宽度，以弥补它在用户屏幕上占据的实际空间。

另一方面，border-box 模型将指定的宽度和高度作为元素在用户屏幕上所占的总空间，而不管向元素添加了多少填充或边框。

```
div {
  box-sizing: border-box;
  width: 200px;
  padding: 50px;
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码示例中，div 将在用户屏幕上占据 200px 的宽度，而它的内容或子元素将只有 100px。所以如果我写了

```
<div>
<span></span>
<div>

div {
  box-sizing: border-box;
  width: 200px;
  padding: 50px;
}

div span {
  width: 100%;
} 
```

Enter fullscreen mode Exit fullscreen mode

跨度的宽度将是 100 像素

[![Representation of border-box example](img/b69034fa448c1c3d49de0537fc5181df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QtShOdWY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3phn1iexj4r6hc1ikl4r.png)

两种模型中的框边距都不会从指定的宽度中减去。举个例子，如果你写了，

```
div {
  box-sizing: border-box;
  width: 200px;
  margin: 50px;
}
div {
  box-sizing: content-box;
  width: 200px;
  margin: 50px;
} 
```

Enter fullscreen mode Exit fullscreen mode

两者做的都一样，翻译到屏幕上的东西没有区别。

内容框模型可能会变得令人困惑，并要求您始终在我的脑海中保留应用于元素的填充和边框值。有了边框模型，你可以保证无论你指定什么宽度，不管填充和边框都保持不变，这样更容易记住。

您通常会在大多数 CSS 项目
中找到这个片段，而不是为每个元素指定`box-sizing`属性

```
*, *::before, *::after {
  box-sizing: border-box;
} 
```

Enter fullscreen mode Exit fullscreen mode

这实际上使得页面上的每个元素都使用了`border-box`盒子模型，并且还将其应用到了`before`和`after`伪元素。

那是一个盒装模型。更多信息，请查看 MDN 的精彩文档[https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing](https://developer.mozilla.org/en-US/docs/Web/CSS/box-sizing)