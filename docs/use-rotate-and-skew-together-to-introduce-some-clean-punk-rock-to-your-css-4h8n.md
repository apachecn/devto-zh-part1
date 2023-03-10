# 同时使用 rotate()和 skew()在你的 CSS 中引入一些干净的朋克摇滚

> 原文：<https://dev.to/brob/use-rotate-and-skew-together-to-introduce-some-clean-punk-rock-to-your-css-4h8n>

[https://www.youtube.com/embed/NIxlqydrRnc](https://www.youtube.com/embed/NIxlqydrRnc)

网页设计行业将很快开始变得非常不同。字面上。

随着我们在 CSS 中获得的所有工具，设计师们将会有新的实验能力。我写了一些关于 CSS 技巧的工具:[用 CSS 网格克服五种设计恐惧](https://css-tricks.com/five-design-fears-vanquish-css-grid/)。

我仍然相信，从 70 年代和 80 年代的朋克摇滚设计中汲取灵感将会成为一种趋势。

如果你想从小处着手，在你的设计中引入一些角度。这是一个简单的技巧，可以在不增加尴尬的空白的情况下调整内容的角度。

## 从一条规则的条纹开始

[![Regular Stripe Image](img/88e702ad6a423c53b30557dba5785081.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QGXCXiXx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bryanlrobinson.cimg/uploads/regular.jpg)

我们都知道这种设计模式:全幅背景和漂亮的宣传内容。

我们设置了非常简单的标记:

```
<section class="stripe">
    <h1>Check this regular stripe</h1>
    <p>If you're wanting to get into punk-rock design, rotated areas are really cool and you'll look hip and cool.</p>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

添加一些非常简单的 CSS，我们就完成了。

```
.stripe {
    background-image: linear-gradient(240deg, #eaee44, #90ec19);
    padding: 5rem;   
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个可爱的有用的内容条纹。当你在那里的时候，你也可以挖掘一个很好的线性梯度。

## 使用 transform: rotate()引入角度

[![Rotate the first stripe](img/3c675b9f6b8a812778b823d26ace43bd.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1wvXv_Xx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bryanlrobinson.cimg/uploads/rotate.jpg)

transform CSS 属性有很多很棒的函数。rotate()是最容易使用的函数之一。它采用一个角度单位，如 45 度，并将元素旋转该角度。正整数表示顺时针旋转，负整数表示逆时针旋转。

```
.stripe {
    background-image: linear-gradient(240deg, #eaee44, #90ec19);
    padding: 5rem;

    transform: rotate(-5deg);
} 
```

Enter fullscreen mode Exit fullscreen mode

你会从照片中注意到这带来了一个问题。这仍然是一个矩形，通过旋转矩形，我们可以看到角。

这感觉不专业，所以我们需要这个来保持与浏览器边缘齐平。

## 【歪斜】(救援)

[![Skew the rotated element](img/7933ce8d5b69c38c9fb5187b86359a56.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C_t1ERaV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bryanlrobinson.cimg/uploads/rotate_skew.jpg)

通过使用我们在 rotate()函数中使用的相同角度，我们可以将元素倾斜回去。这将使元素的左右两边回到它们的起点。

transform 属性可以接受多个函数，所以我们在 CSS 的同一行中应用它。

```
.stripe {
    background-image: linear-gradient(240deg, #eaee44, #90ec19);
    padding: 5rem;

    transform: rotate(-5deg) skew(-5deg);
} 
```

Enter fullscreen mode Exit fullscreen mode

挑剔的设计师会注意到我们实现中的另一个问题。文本现在是倾斜的。这可能是你想要的。扭曲的文本让我有点困扰，所以让我们解开它。

## 取消文本抖动的时间

[![Unskew the text](img/15697503d82e0e20c05751534b052996.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AmqtGI94--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://bryanlrobinson.cimg/uploads/rotate_skew_unskew.jpg)

如果可以的话，我并不热衷于引入新的样式标记，但是通过引入内容容器，我们可以解决文本倾斜的问题。

很有可能，这里有一个容器来设置内容的宽度。

通过应用我们一直使用的负角度倾斜，文本将重新倾斜回它的初始角度。如果需要的话，你也可以用这个方法来取消文本的旋转。

全加价:

```
<section class="stripe">
    <div class="stripe__content">
        <h1>Check this rotation with the straight edges and non-skewed text!!!</h1>
        <p>If you're wanting to get into punk-rock design, rotated areas are really cool and you'll look hip and cool.</p>
    </div>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

完整 CSS:

```
.stripe {
    background-image: linear-gradient(240deg, #eaee44, #90ec19);
    padding: 5rem;

    transform: rotate(-5deg) skew(-5deg);
}
.stripe__content {
    transform: skew(5deg);
} 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们干净的线条和一丝朋克摇滚的味道。[在 CodePen](https://codepen.io/brob/pen/xpNyqE?editors=1100) 上查看这一操作。

如果你还在用以前的方式做事，是时候给事情加点料了。添加角度是在设计过程中添加 cayenne 的一种简单而轻松的方式。

### 与布莱恩保持联系

如果你想及时了解我制作的所有内容，请在这里关注我，同时也关注其他平台。

*   [推特:@brob](https://twitter.com/brob)
*   [YouTube 上我的视频](https://goo.gl/wCeu5C)
*   [我的 Patreon 提前更新](https://patreon.com/brob)
*   [在 peerreviews.dev 上观看或请求设计或代码评审](https://peerreviews.dev)