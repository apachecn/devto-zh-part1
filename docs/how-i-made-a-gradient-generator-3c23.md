# 我是如何制作梯度发生器的

> 原文：<https://dev.to/scottandrews98/how-i-made-a-gradient-generator-3c23>

作为我在 dev.to 上的第一个帖子，我想最好先介绍一下我自己。我叫 Scott Andrews，是英国伍斯特大学计算机专业的二年级学生。我 12 岁开始用 HTML 和 CSS 编程，再也没有回头。我有几个网站客户，即将发布我的第一个 IOS 应用程序。

我首先选择 jQuery 作为我选择的 javascript 框架，开始构建网站。我选择了 jQuery，因为它使我能够快速轻松地隐藏 html 代码部分，并响应悬停事件。jQuery 还添加了简单的动画，非常强大。

在谷歌搜索了一下后，我遇到了堆栈溢出(这是我花 90%时间在互联网上的地方。)能够创建随机十六进制十进制值所需的 javascript 方法。

`randomColourOne = "#000000".replace(/0/g,function(){return (~~(Math.random()*16)).toString(16);});`

一旦价值被创建，它需要被分配到网站的背景。为此，我使用了-webkit-linear-gradient css 样式，它仍然存在兼容性问题，但在 codepen 上运行良好。

`var background = document.getElementById("background");
background.style.backgroundImage = "-webkit-linear-gradient("+ randomColourOne +" , "+ randomColourTwo +")";`

这实际上是 Javascript 的主要部分。完整的代码可以在 codepen 获得。

[https://codepen.io/ScottAndrews/embed/OjEWme?height=600&default-tab=result&embed-version=2](https://codepen.io/ScottAndrews/embed/OjEWme?height=600&default-tab=result&embed-version=2)

T2】

谢谢各位，斯科特·安德鲁斯