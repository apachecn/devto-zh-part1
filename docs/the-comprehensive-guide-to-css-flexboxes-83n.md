# CSS flexboxes 综合指南

> 原文：<https://dev.to/codetheweb/the-comprehensive-guide-to-css-flexboxes-83n>

[![The comprehensive guide to CSS flexboxes](img/4d63886c0bca103c2462fb99d56ef8ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ugf_j3ci--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/cover.jpg)

> 是的，没错。今天的另一个微不足道的封面图片😜

## 入门

通过跟随，你最终会更好地掌握这个主题(也可以进行实验！).接下来，首先创建一个新的项目文件夹，其中包含`index.html`和`style.css`文件。将以下代码添加到您的`index.html` :

```
<!DOCTYPE html>
<html>
    <head>
        CSS flexboxes DEMO
        <link rel="stylesheet" href="style.css">
    </head>
    <body>
        <div id="wrapper">
            <div class="inner_div" id="div1">Div 1</div>
            <div class="inner_div" id="div2">Div 2</div>
            <div class="inner_div" id="div3">Div 3</div>
        </div>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

之后，将下面的代码添加到您的`style.css`文件中:

```
body {
    margin: 0;
}

#wrapper {
    background-color: deepskyblue;
    height: 50vh;
}

.inner_div {
    background-color: whitesmoke;
    padding: 15px;
    border: 2px solid black;
    width: 50px;
    height: 50px;
} 
```

Enter fullscreen mode Exit fullscreen mode

希望你能理解大部分(如果不是全部)的内容。首先，我们将`<body>`边距设置为`0`——这是因为`<body>`有一个默认的边距，这会影响定位和大小。之后，我们给我们的包装 div >一个`background-color`，并将其高度设置为 [`50vh`](https://codetheweb.blog/2017/12/04/css-units/#vw--vh) 。这意味着它将占用 50%的屏幕高度。

最后，我们给包装器中的每个 div 一个`background-color`(这样我们就能看到它们)、一些`padding`(为了我的理智起见)、一个`border`(这样我们就能看到它们的边在哪里)以及一个`width`和`height`。

保存您的文件，并在浏览器中打开您的`index.html`。如果一切正常，它应该是这样的:
[![The result of our base code](img/cd8aa27416ed8a072a32d55b78b1dff4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9AxsbJZ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/base-code.png)

好了，让我们开始有趣的部分吧！

## 什么是 flexbox？

flexbox 由一个父元素(在我们的例子中是`<div id="wrapper">`)和至少一个子元素组成。
[![Our parent element with child elements inside it](img/00fc8c7d4d724a86259a5e5f88079c72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T8JZAJu8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/flexbox-structure.png)

## 创建 flexbox

问题是，我们的包装器实际上还不是 flexbox！我们需要告诉浏览器使用`display: flex`使其成为 flexbox。让我们在 CSS 中这样做:

```
#wrapper {
    background-color: deepskyblue;
    height: 50vh;
    display: flex; /* Add this line */
} 
```

Enter fullscreen mode Exit fullscreen mode

结果:
[![The elements are now ordered horizontally!](img/49a0847ab65b6a645f8079ea1cb74a95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wTWgMhRR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/display-flex.png)

如您所见，元素现在已经被水平排序而不是垂直排序了！默认情况下，flexbox 的子对象是水平排序的，除非另有说明(我们将进一步深入讨论)。

我们走吧！现在我们已经创建了一个 flexbox！让我们开始用它做一些事情...

## 调整内容属性

`justify-content`属性用于在 flexbox 中水平对齐项目。让我们来看看价值观和它们的作用...

### 两端对齐-内容:居中

`justify-content: center`完全按照你的想法去做——它将 flexbox 中的所有项目居中。来试试吧:

```
#wrapper {
    background-color: deepskyblue;
    height: 50vh;
    display: flex;
    justify-content: center; /* Add this line */
} 
```

Enter fullscreen mode Exit fullscreen mode

结果:
[![The items are centered within the wrapper div](img/cba0ca2d9a8ef4805392fe98f07b324b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Exo-nCBP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/justify-content-center.png) 
就这么简单！

### 对齐-内容:弹性-开始

`justify-content: flex-start`将项目对齐到 flexbox 的左侧。来试试吧:

```
#wrapper {
    background-color: deepskyblue;
    height: 50vh;
    display: flex;
    justify-content: flex-start; /* Change this line */
} 
```

Enter fullscreen mode Exit fullscreen mode

结果:
[![The items are left-aligned within the wrapper div](img/49a0847ab65b6a645f8079ea1cb74a95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wTWgMhRR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/display-flex.png) 
如你所见，这些项目现在对齐到 flexbox 的左边缘(flexbox 的‘开始’，因此得名`flex-start`)。

### 对齐-内容:伸缩-结束

`justify-content: flex-end`与`flex-start`相同，除了它将项目对齐右边缘(flexbox 的“末端”)。让我们更新我们的 CSS，看看会发生什么:

```
#wrapper {
    background-color: deepskyblue;
    height: 50vh;
    display: flex;
    justify-content: flex-end; /* Change this line */
} 
```

Enter fullscreen mode Exit fullscreen mode

结果:
[![The items are right-aligned within the wrapper div](img/0d2475d0add0e5503ad7bde85ae024f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7uGDptsA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/justify-content-flex-end.png)

### 两端对齐-内容:空格-左右

`justify-content: space-around`分隔 flexbox 中的项目，使每个项目在周围有相同的空间*。让我们尝试一下，以便更好地理解:* 

```
#wrapper {
    background-color: deepskyblue;
    height: 50vh;
    display: flex;
    justify-content: space-around; /* Change this line */
} 
```

Enter fullscreen mode Exit fullscreen mode

[![The items all have an even amount of space AROUND them](img/e67abc6cbeddde4baa92e93f127a3281.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--D39Jmxfj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/justify-content-space-around.png)

这里有一个图表，这样您可以更容易地看到间距，您会看到每个项目周围的空间是相同的:

[![The items all have an even amount of space around them (diagram)](img/1c363f4643f46eb4cef719c720afc4ad.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Ir3xdWDK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/justify-content-space-around-diagram.png)

### 两端对齐-内容:空格-之间

`justify-content: space-between`在之间以均匀的间距*来分隔项目，而不是在*周围以均匀的间距*来分隔项目。这一开始看起来很混乱，但是一旦我们尝试了，区别就很明显了。所以，我们来试试看吧:* 

```
#wrapper {
    background-color: deepskyblue;
    height: 50vh;
    display: flex;
    justify-content: space-between; /* Change this line */
} 
```

Enter fullscreen mode Exit fullscreen mode

结果:

[![The items all have an even amount of space BETWEEN them)](img/5c79c048cde3dc102fbbdb3dae898322.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qvskXXrJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/justify-content-space-between.png)

如你所见，`space-around`和`space-between`有很大的区别！这张图表将帮助你想象`space-between`是如何不同的:

[![The items all have an even amount of space between them (diagram)](img/b2c17e75b4aa51fbda649368ca7d35a5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8raTjObt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/justify-content-space-between-diagram.png)

现在个物品之间的空间*是相等的，但是每一个*周围的空间*却不相等。*

### 对齐-内容:空间-均匀

真的吗？那些`space`属性中的另一个？是的，没错- `justify-content: space-evenly`就像`space-between`，但是包括侧面。这意味着每个项目之间的间距是相同的，但也与外部项目和边缘之间的间距相同。让我们试一试，看看会发生什么:

```
#wrapper {
    background-color: deepskyblue;
    height: 50vh;
    display: flex;
    justify-content: space-evenly; /* Change this line */
} 
```

Enter fullscreen mode Exit fullscreen mode

结果:

[![The items all have an even amount of space between them, and also to the edges](img/ed4b00e0375d57ae651942444146a2b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zylz_5hT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/justify-content-space-evenly.png)

如您所见，每个项目之间的间距以及项目和边缘之间的间距是相等的。这张图表(希望)能让事情变得清晰一些:

[![The items all have an even amount of space between them, and also to the edges (diagram)](img/d962c018641ec601710183e688a18503.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--YF-qDxyf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/justify-content-space-evenly-diagram.png)

虽然它可能看起来很像`space-around`，但实际上不是。以下是它们的间距比较:

[![space-around and space-evenly are actually very different](img/ce2c7eb6c83a021fdae2d041f749047c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SuYMB_yA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/justify-content-space-around-evenly-comparison.png)

唷！以上是`justify-content`上的所有内容——希望不会太混乱！虽然本文还涉及了更多内容，但是不要担心——它并没有那么复杂！现在让我们转向一些不同的东西...

> 注意:`justify-content`有更多的值，但这些是您真正需要关心的唯一值。

## 对齐项目属性

`align-items`属性用于在 flexbox 中垂直对齐项目。它接受`center`、`flex-start`和`flex-end`的值。你已经知道这些是干什么的了！但是注意，因为是垂直的，`flex-start`是靠上而不是靠左对齐，`flex-end`是靠下而不是靠右对齐。让我们试一试:

```
#wrapper {
    background-color: deepskyblue;
    height: 50vh;
    display: flex;
    justify-content: space-evenly;
    align-items: center; /* Add this line */
} 
```

Enter fullscreen mode Exit fullscreen mode

结果:
[![The items are now centered vertically!](img/465f4ec055be1e083d0b8485e25f4fc7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p9k61HEx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/align-items-center.png)

下面是`flex-start`和`flex-end`的样子:
[![align-items: flex-start and align-items: flex-end](img/eff06cfb6b713f9ae28ef6d77a4aa9f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bIkzcufK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/align-items-flex-start-flex-end.png)

请记住，您不能使用`space-around`、`space-between`和`space-evenly`作为`align-items`属性的值。

> 注意:`align-items`也有更多的值，但这些是您真正需要关心的唯一值。

## 完美对中！

在 flexboxes 出现之前的黑暗日子里，垂直和水平居中是一件痛苦的事情，并且需要丑陋的黑客。幸运的是，现在它就像给父母三行 CSS 代码一样简单！

(不要把它添加到你当前的 CSS 文件中，但是如果你想的话，你可以以后再玩😉)

```
#parent {
    display: flex;
    justify-content: center;
    align-items: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

这只是 flexboxes 的众多令人惊叹的用途之一。

继续前进。

## 伸缩包装属性

如果你的物品比较宽，一行装不下会怎么样？我们来看看——把`.inner_div`的宽度改成`600px` :

```
.inner_div {
    background-color: whitesmoke;
    padding: 15px;
    border: 2px solid black;
    width: 600px; /* Change this line */
    height: 50px;
} 
```

Enter fullscreen mode Exit fullscreen mode

结果:
[![The items are still all on one line, and have been shrunk!](img/f3aca964aa2a119743ea9c2f9a719aee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IRKVuDvJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/items-too-wide.png) 
如你所见，物品都停留在一行上，占据了它们被允许的最大宽度(只有`422px`，比`600px`少了很多！).

> 注:根据您的屏幕宽度，这些尺寸可能会有所不同。

见鬼，你甚至可以将宽度设置为`100000000px`，它仍然会占用相同的空间！(不服气可以试试)。

如果我们想在项目太大时将它们放到新的一行，我们可以使用`flex-wrap`属性——试试看！

**(如果把宽度设为`100000000px`，先改回`600px`)**

```
#wrapper {
    background-color: deepskyblue;
    height: 50vh;
    display: flex;
    justify-content: space-evenly;
    align-items: center;
    flex-wrap: wrap; /* Add this line */
} 
```

Enter fullscreen mode Exit fullscreen mode

结果:
[![Yay, the items go over onto multiple lines now!](img/e032cc069cb45985d484e7604538e1a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i_RIgw90--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/flex-wrap.png)

如您所见，项目现在变大了(它们的实际大小，`600px`)。

`flex-wrap`还允许我们创建适应多种屏幕尺寸的布局——这被称为 [*响应式设计*](https://codetheweb.blog/newsletter/) 。看一看:
[![The page adapts to different screen sizes](img/e5121a16a95679402f5c061a5b67def8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--393IM7cC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/flex-wrap-responsive.gif)

这样，这些项目就不会奇怪地调整大小，也不会脱离屏幕边缘。

> ## 恢复正常...
> 
> 在继续之前，让我们将演示恢复正常:
> 
> ```
> body {
>     margin: 0;
> }
> 
> #wrapper {
>     background-color: deepskyblue;
>     height: 50vh;
>     display: flex;
>     justify-content: space-evenly;
>     align-items: flex-start;
>     /* Remove the flex-wrap line */
> }
> 
> .inner_div {
>     background-color: whitesmoke;
>     padding: 15px;
>     border: 2px solid black;
>     width: 50px; /* Change this back to 50px */
>     height: 50px;
> } 
> ```

## 伸缩方向属性

整个过程中，我们的物品一直在水平移动。但是，如果我们想让他们垂直*前进*呢？

这就是`flex-direction`属性的用途。让我们尝试让我们的项目垂直...

```
#wrapper {
    background-color: deepskyblue;
    height: 50vh;
    display: flex;
    justify-content: space-evenly;
    align-items: flex-start;
    flex-direction: column; /* Add this line */
} 
```

Enter fullscreen mode Exit fullscreen mode

结果:
[![The items are now arrange vertically](img/32dceb2e7e65fe172482f92463d6f097.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SQlVEU2E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/flex-direction-column.png)

不过有一点你需要记住——当使用`flex-direction: column`时**，`justify-content`和`align-items`是颠倒的。**下面是一个图表:

[![align-items and justify-content are reversed](img/6765ebac15ac4027becb145432f559ac.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xt47xwfc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/flex-direction-axes.png)

这就很混乱了！然而，你将不得不习惯它——记住当你旋转方向时，你也旋转了属性。

## 订单属性

> 首先，删除您的`flex-direction: column`行——它会妨碍演示该属性:
> 
> ```
> #wrapper {
>     background-color: deepskyblue;
>     height: 50vh;
>     display: flex;
>     justify-content: space-evenly;
>     align-items: flex-start;
>     /* Remove the flex-direction line */
> } 
> ```

好吧，我们走吧...这是我们今天最后的财产！

还记得我在文章开头提到的你可以使用 CSS flexboxes 对 HTML 元素重新排序吗？这是怎么回事。能够对元素重新排序是革命性的——以前，你必须做一些难看的`position`修改，在某些情况下很容易出错。那么，我们该怎么做呢？

与目前为止的其他属性不同，`order`属性应用于实际的*项目*，而不是父包装器。`order`属性接受任何整数作为值。

订购 flexbox 中的物品时，每个物品都有*位置*。第一个项目在位置`0`，第二个项目在位置`1`等。(编程中的大多数列表从`0`而不是`1`开始)

让我们试着让顺序变成 Div 2，Div 1，Div 3。我们可以这样做:

```
#div1 {
    order: 1;
}
#div2 {
    order: 0;
}
#div3 {
    order: 2;
} 
```

Enter fullscreen mode Exit fullscreen mode

结果:
[![The order is now Div 2, Div 1, Div 3](img/9a85155ae5f7a92110a4f31801f74548.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MicJnDkh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/css-flexboxes/order-213.png)

耶！现在我们的订单被更改了！这就是`order`属性的美妙之处——它可以改变屏幕上元素的顺序，只需要使用 CSS！

如果你想的话，你现在可以多考虑一下订单。

## 游戏时间！🕹🕹🕹 🐸

如果你想巩固今天所学的知识，并享受一点极客的乐趣，我强烈推荐你去玩 [Flexbox Froggy](http://flexboxfroggy.com/) 。有 24 个级别，你必须使用 CSS flexboxes 将青蛙放置在正确的 lilypads 上。这实际上是我在学习时最先发现 flexboxes 的地方之一，它真的很有帮助。

另外，如果你想了解 flexboxes 的一些功能，请看这里的。

但是先别走！我们仍然需要做总结，我请你友好地[分享这个](https://codetheweb.blog/2017/12/05/css-flexboxes/#share)并祝贺你，即使我可能实际上并不认识你！

## 结论

干得好！今天这篇文章很长，因为内容太多了。希望你觉得有用！如果你这样做了，我真的很希望你[分享文章](https://codetheweb.blog/2017/12/05/css-flexboxes/#share)或者[注册时事通讯](https://codetheweb.blog/newsletter/)来接收你收件箱里的新帖子。如果你做了这些事情中的任何一件，你真的很棒，应该得到一个墨西哥玉米卷🌮 🌮 🚀

> **重要提示:不要忘记，我们今天学习的所有属性*只适用于 flexboxes】。因此，不要试图去其他地方使用它们😉***

如果你有任何反馈，问题，需要帮助或者只是想打声招呼，请在下面的评论中留言，我会回复你的！

下次见，届时我将利用我们今天学到的一些东西，写一篇关于如何制作整版英雄形象的专题文章😉)

继续编码，祝你有美好的一天，到时见！(别忘了还有 [Flexbox Froggy](http://flexboxfroggy.com/) 🐸 😉)

> ### 想要更多这样的帖子？[注册我的简讯](https://codetheweb.blog/newsletter/)。
> 
> 我一直在努力学习这些教程，但是到目前为止还没有赚到任何钱，所以如果你注册的话，我会非常感激；)

这篇文章最初发表在 [Code The Web](https://codetheweb.blog/) 上，这是一个有趣且易于理解的博客，帮助你学习 Web 开发🚀
学:[HTML](https://codetheweb.blog/learn/html)|[CSS](https://codetheweb.blog/learn/css)|[JavaScript](https://codetheweb.blog/learn/javascript)