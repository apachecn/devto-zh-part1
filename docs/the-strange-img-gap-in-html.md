# HTML 中奇怪的缺口

> 原文：<https://dev.to/christiankaindl/the-strange-img-gap-in-html>

到目前为止，我在我的短开发游戏中遇到了很多挫折，其中一个最近在我做我的一个小项目的时候再次闪过我的眼睛。

正是在使用 HTML `<img>`标签时这种奇怪的差距让我已经疯狂了好几次:

[![Image gap](img/f7a7d9fde0432dd98f6db05e2f559cb3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2y3pLLQA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hrf2l4jwoxsdfdxxlz7k.png)

但这次不是！

# 解

这个问题的推理和解决方案实际上非常简单，与盒子的文本有关。

等等，什么？图中无文字！

振作起来:

事实证明，`img`元素默认是行内元素。这意味着它们基本上与文本一样流动。因此，图像的下边缘与用`font-size`属性指定的字体基线对齐。

谜团解开了！以下是解决此问题的方法:

## 方案 1

在您的图像上，将`vertical-align`设置为`bottom`、`top`、`text-top`、`text-bottom`或`middle`

## 方案二

将你的图像设置为`display: block`(然后你还必须为你的图像指定一个宽度，否则所有的东西都会落入下一行)

你也可以使用更现代的技术来解决这个问题。因此，将`display`设置为`flex`或`grid`(也需要设置宽度)，或者它们的内嵌对应物`inline-flex`和`inline-grid`也可以工作，同时像`display: block`一样提供更多的灵活性(当应用于图像容器时也可以工作)。

## 方案三

不要一起使用图片(让你的网站更快)

## 方案 4

也许这正是你想要的！在基线上对齐文本流中的图像或小图标再简单不过了！

## 方案 5

将图像容器(父)设置为`line-height: 0`(当容器是`inline`元素时无效)或`font-size: 0`(总是有效)

## 解决方案 6

在图像上，将`float`设置为`right`、`left`、`inline-end`或`inline-start`

* * *

最终，“神秘的形象差距”一点也不神秘，尽管如此，这种简单的挫折会导致什么样的结果还是很有趣。毕竟，我现在对内联元素的行为有了更透彻的理解。

如果这还不够，这里还有一些资源:

*   [http://gtwebdev.com/workshop/gaps/image-gap.php](http://gtwebdev.com/workshop/gaps/image-gap.php)
*   [https://developer . Mozilla . org/en-US/docs/Images % 2C _ Tables % 2C _ and _ mysteric _ gap](https://developer.mozilla.org/en-US/docs/Images%2C_Tables%2C_and_Mysterious_Gaps)
*   或者只是在你最喜欢的搜索引擎中搜索“html img gap”或“img tag gap ”,让上千个堆栈溢出问题沉入你的脑海

感谢您的阅读:)

* * *

*编辑:*

如果你想了解更多关于 HTML 布局的细节，你也可以在 Mozilla 开发者网络上查看一下 T2 的 HTML 参考资料。

*一些延伸阅读:*

*   *[HTML 行内/块元素 cheatsheet](https://developer.mozilla.org/en-US/docs/Learn/HTML/Cheatsheet)*
*   *[块格式化上下文](https://www.smashingmagazine.com/2017/12/understanding-css-layout-block-formatting-context/)*
*   *[CSS `display`文档](https://developer.mozilla.org/en-US/docs/Web/CSS/display)*
*   *[CSS 上的文章`display`价值观](https://quirksmode.org/css/css2/display.html)*