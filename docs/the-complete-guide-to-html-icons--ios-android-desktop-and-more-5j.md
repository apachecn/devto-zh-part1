# HTML 图标的完整指南——iOS、Android、桌面和更多…

> 原文：<https://dev.to/codetheweb/the-complete-guide-to-html-icons--ios-android-desktop-and-more-5j>

[![The complete guide to HTML icons — iOS, Android, desktop and more…](img/d48754b481a601228ade63d6af917717.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xWaEx7Nd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/cover.png) 
在这篇文章中学习如何使用 HTML 为 iOS、Android、桌面等制作图标…

## 入门

请随意跟随！您可能没有我将要谈到的所有设备、操作系统或浏览器，但是您至少能够测试一两个😉

首先用下面的基本代码创建一个 HTML 文件:

```
<!DOCTYPE html>
<html>
    <head>
        HTML Icons
    </head>
    <body>
        <h1>HTML icons!</h1>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

在浏览器中打开它——它应该是这样的: [![An HTML page with the words "HTML icons!"](img/bbededd3cc456e1e5e358055e828c753.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P5KQX8kd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/base-code.png)

现在你已经准备好了！

## 图标

在今天的例子中，我们将使用我制作的华夫饼图标(当然，上面还有冰淇淋🍦 😉) [![Waffle icon](img/1249b171251b47ef6c2b32416848bba2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vwsrj0F9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/icon1200.png)

> 那看起来不好吃吗！
> 
> ## 浏览器标签图标(favicons)

如果你现在看一下你标签上的小图标(这些图标也被称为 favicons)，它只会给我们一个无聊的默认图标——没人想要那个！

[![The default 'page' icon](img/b0cb2e527044bacedd7c347486dc8040.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Fhabvz7E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/no-tab-icon-zoom.png)

> 请注意，根据您使用的浏览器，选项卡和默认图标可能会略有不同。这篇文章中的这个和大多数其他浏览器截图都是在 Google Chrome 中拍摄的。

所以，我们自己加一个吧！只需在您的`<head>` :
中添加这一行代码

```
<link rel="icon" type="image/png" sizes="64x64" href="https://codetheweb.blog/assets/img/posts/html-icons/icon64.png"> 
```

Enter fullscreen mode Exit fullscreen mode

`type`属性取决于我们使用的图像。在这种情况下，我们使用的是一个`.png`文件，所以我们将把我们的`type`设置为`image/png`。您还可以使用其他的`type`,例如用于`.ico`文件的`image/ico`。

[点击此处查看支持的文件类型列表。](https://en.wikipedia.org/wiki/Favicon#File_format_support)

另外，确保你的图标是`16px`的`16px`、`32px`的`32px`或`64px`的`64px`。您需要确保*实际图像*具有这些尺寸以及`sizes`属性——您可以在线调整图像的大小。请注意，其他大小也可以，但并非所有浏览器都支持。在这种情况下，我们的图像是`64px`乘`64px` -我推荐你使用这个尺寸，因为它是三个中分辨率最高的。

反正这里是结果: [![Our favicon now shows up!](img/0ca734174bddc24bd1c09ca82c05167e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4SxqC1QY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/tab.png)

现在我们的页面标题旁边有一个图标！🎉 🎉[![A close up of the favicon](img/bc02d7e245ec15da5ade6e9e7491128b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--F1iuGa5A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/tab-zoom.png)

## Safari 主屏幕和书签图标

如果我们[在 iOS 上把我们的网站添加到主屏幕](https://www.wikihow.com/Add-a-Link-Button-to-the-Home-Screen-of-an-iPhone)上，默认情况下会给我们一个非常难看且大小怪异的截图: [![A very ugly and weirdly-sized screenshot as the website icon](img/7a217cd7652a4e21dc5d9e29072fab2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--53VKVl9o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/safari-default.png)

让我们添加我们自己的图标吧！为此，在您的`<head>` :
中，将此行添加到您的 favicon 行下方

```
<link rel="apple-touch-icon" sizes="180x180" href="https://codetheweb.blog/assets/img/posts/html-icons/icon180.png"> 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这与我们声明 favicon 的方式非常相似，除了我们使用`apple-touch-icon`作为`rel`属性。确保你的图标在`180px`前一直是`180px`。

现在，如果我们再次将它添加到主屏幕，我们会得到一个更好的结果！[![Now our waffle icon is on the home screen!](img/6c73f713589b158fee7a352e9689a34c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CAlKlcp---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/safari-custom.png)

这也给了我们网站一个 iOS 上 Safari 书签的图标: [![Bookmark icon on iOS](img/2c1f889f536e8930a4d5dc8975a6ed8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vWUz2Al_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/ios-bookmark-icon.png)

而在 Mac 上: [![Bookmark icon on Mac](img/f103c665ee3aa94914998ec95ec0ae06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oKofDNq5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/mac-bookmark-icon.png)

## 安卓图标

还可以使用 Android 上的谷歌 Chrome 将网站添加到主屏幕上。幸运的是，我们不需要只为 Android 指定图标！它要么使用图标，要么使用`apple-touch-icon`图标(如果你有的话)。

## 窗口平铺

在 Windows 上，如果你还不知道，有一些东西叫做磁贴: [![Windows tiles](img/b9966252369fafd847a52e8d169150bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4nnOWw7j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/windows-tiles.jpg)

在 Windows 手机上也用: [![Windows phone tiles](img/67c6050b0d32be91b224e46b620e33da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ulCZg4EM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/windows-phone-tiles.png)

正如你在上面的照片中看到的，我们还需要创建一个矩形版本的标志，如果瓷砖是矩形的。下面是我做的长方形版本的华夫饼: [![Rectangular waffle icon](img/d5024676e424bc736af6ee84669a8cdf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k-eRSK-1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/icon-rect-2480.png)

在我们的 HTML 中，我们需要定义这个图标以及三个正方形图标大小:`70px`乘以`70px`，`150px`乘以`150px`，以及`310px`乘以`310px`。我们的矩形图标需要有`310px`宽和`150px`高。

不过有一点很重要:当制作 Windows 磁贴的图标时，一定要有透明的背景。

下面是我们的图标，但背景是透明的: [![Square icon with transparent background](img/b589e7a3193f7071d3c1f0332de97b29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HkrNsR3j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/icon-trans-1200.png) [ ![Rectangular icon with transparent background](img/2a7b882f7612c5fe870be89fc0819180.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--54HuAzZ9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/icon-rect-trans-2480.png)

这是 HTML——把它添加到你的 Safari 图标和 favicon 图标下面的`<head>` :

```
<meta name="msapplication-square70x70logo" content="https://codetheweb.blog/assets/img/posts/html-icons/icon70.png">
<meta name="msapplication-square150x150logo" content="https://codetheweb.blog/assets/img/posts/html-icons/icon150.png">
<meta name="msapplication-wide310x150logo" content="https://codetheweb.blog/assets/img/posts/html-icons/icon310.png">
<meta name="msapplication-square310x310logo" content="https://codetheweb.blog/assets/img/posts/html-icons/icon-rect-310.png"> 
```

Enter fullscreen mode Exit fullscreen mode

这些与我们到目前为止讨论的图标类型略有不同，因为它们使用了`<meta>`标签而不是`<link>`标签。

但是如果图标有透明的背景，看起来会不会很奇怪？**是的。**它们需要透明背景的原因是为了让窗口可以在下面涂上颜色。以这个图块为例: [![Map icon tile](img/88b5abe90b194c0c200a821111bb315d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3CKameHN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/map-tile.png) 这里，地图图标将是图像(带有透明背景)，然后 Windows 将在它下面放置粉色/紫色。但是我们如何告诉窗口哪种颜色呢？这就是`msapplication-TileColor` meta 标签派上用场的地方。在这种情况下，我们的华夫饼图标背景[十六进制颜色](https://codetheweb.blog/2017/11/15/basic-css-properties/#hex-values)是`#2e2e2e`。考虑到这一点，在`<head>` :
中我们的图标下面添加这条线

```
<meta name="msapplication-TileColor" content="#2e2e2e"> 
```

Enter fullscreen mode Exit fullscreen mode

下面是图标和磁贴颜色的结果元标签: [![The square and rectangular icons as Windows tiles](img/2e1153417997f9d0f4a7ee7c946ded62.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rXf62PMk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://codetheweb.blog/assets/img/posts/html-icons/custom-windows-tiles.png)

## 如果你太懒…😴

有一个很棒的网站叫做 [Real Favicon Generator](https://realfavicongenerator.net/) ，它可以为你生成 Favicon、Safari 图标、Windows 图标等等。**然而，我建议你不要使用这个，直到你对自己做这件事很有信心** -否则，你永远也学不会。也就是说，如果你需要，你可以随时查阅。所以这取决于你。

## 结论

呜！今天就到此为止。希望你学到了一些东西！如果你这样做了，我很乐意你分享它或者[注册时事通讯](https://codetheweb.blog/newsletter)在你的收件箱里得到新的帖子。如果你做到了这些事情中的任何一件，你就太棒了，应该得到一份太空玉米卷，如果那是一件东西的话！😎 🌮 🚀

我今天谈到了许多不同平台的图标，你不必使用所有的图标——尽管这是一个好主意。但是作为开始，你可以试着习惯 favicons(第一部分)。

如果你需要帮助，有反馈或想打招呼👋如果你在下面的评论中这样做，我会很高兴。

下一次，我将在[快速参考网站上发表一篇专题文章，帮助你踏上编码之旅](https://codetheweb.blog/2017/12/16/web-development-reference-sites/)。到时候见！

> ### 想要更多这样的帖子？[注册我的简讯](https://codetheweb.blog/newsletter/)。
> 
> 我一直在努力学习这些教程，但是到目前为止还没有赚到任何钱，所以如果你注册的话，我会非常感激；)

这篇文章最初发表在 [Code The Web](https://codetheweb.blog) 上，这是一个有趣且易于理解的博客，帮助你学习 Web 开发🚀
学:[HTML](https://codetheweb.blog/learn/html/)|[CSS](https://codetheweb.blog/learn/css/)|[JavaScript](https://codetheweb.blog/learn/javascript/)