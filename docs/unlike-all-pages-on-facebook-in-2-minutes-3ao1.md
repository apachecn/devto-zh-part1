# 不像 facebook 上的所有页面在 2 分钟内

> 原文：<https://dev.to/parthchokshi/unlike-all-pages-on-facebook-in-2-minutes-3ao1>

[![](img/748ae7d77d690b148b5b65bc2142b79c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PlHH4Z6j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A9qE6ABQsvsOKNi3V6KSx2w.jpeg)

我在寻找一种方法，如何一次性地不喜欢脸书的所有页面。在控制台中尝试了一些不同的东西并查看了一些 chrome 扩展后，我发现只需一些命令就可以一次完成所有页面。下面是怎么做的。

首先转到你的个人资料中的“喜欢”部分。以下是快照帮助:

<figure>[![](img/7d2743a9c713d249994a4ebf451190fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5VWS9t1i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/983/1%2AjZOySnT7Prwq1SMOk-eZuQ.png) 

<figcaption>喜欢小节</figcaption>

</figure>

单击喜欢链接。您将被重定向到“喜欢”页面。

然后右键点击你的网页上的任何地方，并点击菜单中的检查链接。它应该会在你的浏览器下面或侧面打开一个部分。

<figure>[![](img/9631c72d4a10fc044f288254468c4ca2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TyydJ_rB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/986/1%2Afr-DGd3iBGVT7B3SO0WinA.png) 

<figcaption>开发者工具</figcaption>

</figure>

类似上面的东西。点击控制台，你会发现一个大大的警告，上面写着“停止”。忽略它。(相信我！:D)

现在编写如下命令:

```
// Get hold of all 'Liked' Buttons
var buttons = document.getElementsByClassName('PageLikedButton'); 
```

Enter fullscreen mode Exit fullscreen mode

<figure>[![](img/6e540f134eba99334abe0e7e8d13b8d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rF8Jsv9Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/984/1%2AO4PBrxmMZ030Kfo8nHuWNw.png) 

<figcaption>代码为</figcaption>

</figure>

的控制台截图

按回车键。然后写下这个命令:

```
// trigger click on all 'Liked' Buttons, to get unlike popovers
for(let button of buttons){button.click();}; 
```

Enter fullscreen mode Exit fullscreen mode

<figure>[![](img/d6eb7ca156738c4a5703cc1d1dad03db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rdR35SLg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/986/1%2AhQZbrXcG8EswHIB6eL3Hbg.png) 

<figcaption>控制台中的另一代码</figcaption>

</figure>

这可能会使您的屏幕冻结一段时间。但是别担心，坚持住，不要试图刷新页面。原因是，它触发了页面上所有喜欢的按钮的点击事件。如果你想与所有页面不同，滚动到页面的最底部——你不能再向下滚动的部分。这是为了在页面视图上显示所有'**喜欢的'**按钮。页面视图中的按钮只会得到点击事件，因为其他页面还没有加载到页面中，因为你还没有向下滚动。只有当你开始向下滚动的时候才会加载。

如果你只是想尝试一下，你不必向下滚动到最底部。只需加载喜欢页面并运行命令。

现在是触发点击所有**的时候了，不像**弹出窗口，它会在运行上述命令后出现。所以要一下子不像他们所有人:

```
// Get hold of all the unlike links
var unlikes = document.getElementsByClassName('itemLabel');
// trigger click on all unlike popovers
for(let unlike of unlikes){unlike.click();} 
```

Enter fullscreen mode Exit fullscreen mode

<figure>[![](img/4ca557cb12d2c5e8c132a67267c8ec8f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--17wVA4uP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/982/1%2AU_a7roqEyDFPY-I5r54FOA.png) 

<figcaption>最终命令</figcaption>

</figure>

所以我们触发点击所有同时出现的不同链接。这应该不同于所有的网页，目前在视口。

**警告**:这将不同于当时页面上出现的所有页面。

但是，嘿，我们总是可以挽回的，不是吗？？！！是的，我们可以。

只需运行以下两个命令。这将像你所有的网页在一个镜头。

```
var buttons = document.getElementsByClassName('PageLikeButton');
for(let button of buttons){button.click();}; 
```

Enter fullscreen mode Exit fullscreen mode

答对了。是的，页面又被喜欢回来了。试试看。

我还写了一个博客，从 facebook 上删除所有广告商和定向广告。

[在 2 分钟内从 facebook 上删除所有广告商和定向广告](https://dev.to/parthchokshi/remove-all-advertisers-and-targeted-ads-from-facebook-in-2-minutes-4am6)