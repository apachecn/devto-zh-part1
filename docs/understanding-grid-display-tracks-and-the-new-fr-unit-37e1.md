# 了解网格显示、轨道和新的 fr 单元。

> 原文：<https://dev.to/magnificode/understanding-grid-display-tracks-and-the-new-fr-unit-37e1>

好的，所以我将要写关于新的时髦的 CSS 网格属性和它的所有组件。我将试着一点一点地做这件事，这样就不会写一篇包含所有 18 个新特性的博文。

我们去找罗林。

### 显示

有三个新值与显示属性相关联，`grid`、`inline-grid`和`subgrid`。前两个让人想起我们在过去看到的 flexbox。`grid`值生成一个块级网格元素，而`inline-grid`就像它所说的那样，生成一个内嵌级网格元素。然而，子网格需要更多的解释。

#### 子网格

子网格简单明了的定义可以在 [CSS-Tricks 完整网格指南](https://css-tricks.com/snippets/css/complete-guide-grid/)中得到最好的总结

> **subgrid** -如果你的网格容器本身就是一个网格项目(即嵌套网格)，你可以使用这个属性来表明你希望它的行/列的大小取自它的父元素，而不是指定它自己的。

不幸的是，正如 CSS 网格大师 Rachel Andrew 指出的那样，[子网格被移到了 CSS 网格规范](https://rachelandrew.co.uk/archives/2017/03/16/subgrid-moved-to-level-2-of-the-css-grid-specification/)的第二层。

举例来说，看看下面的代码笔演示:

请看[码笔](https://codepen.io)上 Dominic Magnifico([@ magnifice code](https://codepen.io/magnificode))的笔 [bRNGoz](https://codepen.io/magnificode/pen/bRNGoz/) 。

subgrid 将有效地做的是消除我们在每个单独的`.grid-item`上定义`grid-template-columns`的需要。它将继承我们提供给`.grid`父节点的值，从而保持我们的代码简洁明了。然而，在网格级别 2 发布之前，我们必须使用上面显示的方法。

让我们处理一下我们在这个例子中看到的另外两个属性，以及一些附加的语法。

### 网格-模板-列(和行)。又名轨道。

这是 CSS 网格的面包和黄油。在我们告诉一个容器成为`display: grid;`之后，我们需要告诉它我们希望其中的网格项目如何表现。通过将`grid-template-columns`或`grid-template-rows`应用到包装器中，我们可以定义一个模板来定义我们希望它的孩子如何表现。这里有一个简单的例子:

```
<div className="grid">
    <div className="grid-item">Sweet Grid!</div>
    <div className="grid-item">Sweet Grid!</div>
    <div className="grid-item">Sweet Grid!</div>
  </div> 
```

Enter fullscreen mode Exit fullscreen mode

和关联的 CSS。

```
.grid {
    display: grid;
    grid-template-columns: 300px 300px 300px;
  } 
```

Enter fullscreen mode Exit fullscreen mode

上面 CSS 中的每个`300px`定义都与一列相关。这种格式适用于任意数量的列或行(在 [CSS Grid-Speak](https://www.w3.org/TR/css-grid-1/#grid-track-concept) 中也称为 tracks)。

### 唉，打了这么多字

我敢肯定，你正坐在电脑前想“哎呀，如果你有一大堆曲目，那就要打很多字了。”

[![ugh](img/7b272eee267c6203ef08e1095e5de4f4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vw-Ee3yY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://bukk.it/ugh.gif)

孩子，你运气怎么样？在`grid-template-columns`属性中，我们可以利用一个叫做`repeat()`的漂亮函数。正如你可能在上面的 CodePen 例子中看到的，我们的网格包装器有这个声明`grid-template-columns: repeat(3, 1fr);`。我打赌你能想出如何使用这个功能。函数中的第一个整数表示我们希望重复以下轨道大小调整的次数。我们传递给函数的第二个参数是大小。这将引导我们进入下一部分，即`fr`单元。

### 很漂亮`fr`很酷吧？

很晚了，这篇博文越来越长，我对双关语越来越热心了。让我们结束这一切，嗯？我们最后要谈的是新的`fr`单元。这个单元允许我们定义灵活的长度，并表示网格容器中自由空间的一部分。

*需要注意的一件重要事情*与 fr 单位。与 px、em 或百分比不同，fr 单位不是定义为长度。这意味着它不能与 px、em 或百分比在类似于`calc()`的函数中结合使用。

[规范本身](https://www.w3.org/TR/css-grid-1/#fr-unit)详细说明了定义可用空闲空间所进行的计算。但是在我的脑海里，它可以归结为:将我们的`grid-template-column`轨道大小设置为`1fr`会根据容器的宽度给我们相等比例的网格项目。

我们能够将小数与 fr 单位结合使用。在我们的例子中，如果我们把我们的值改为，`.5fr`什么都不会改变。这是因为我们的三个项目中的每一个都应该保持比例。因为一个轨迹有三个项目，除非我们告诉他们小于`.33333fr`，否则他们会保持比例。

从一般意义上讲，谈论 CSS 网格是非常困难的，因为规范中有太多的 T1。但希望这个小得足够让你消化。亲爱的读者，老实说，这篇博文对你对我都一样重要。当我在网格中蹒跚而行时，多写一些这样的帖子应该会很有趣。

[![the grid](img/62789beb0f95a46ba61bad4df3ec414e.png)T2】](https://i.giphy.com/media/oSYflamt3IEjm/giphy.gif)