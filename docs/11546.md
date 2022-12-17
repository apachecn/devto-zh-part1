# 如何检查 jQuery 版本？

> 原文：<https://dev.to/moreonfew/how-to-check-jquery-version-dl9>

我们中的许多人可能遇到过这样的场景，我们希望基于加载的 jQuery 的**版本做一些事情。具体来说，我说的是这样的场景，人们可能希望**以编程方式**知道 jQuery 的版本，如果版本是 1.3.2，那么也加载一些其他文件或做一些其他事情。嗯，我必须说，**识别 jQuery 的版本**是一个非常容易的过程，让我们来看看怎么做。**

您可以使用以下命令来了解加载的 jQuery 版本:

```
alert("jquery version = "+ $.fn.jquery);

//or 

alert("jquery version = "+ jQuery.fn.jquery);

//or 

alert("jquery version = "+ $().jquery); 
```

Enter fullscreen mode Exit fullscreen mode

## 如何在控制台检查 jQuery 版本？

通过 firebug 的控制台或任何其他基于浏览器的控制台检查 jQuery 的版本也非常容易。您可以通过在控制台中键入上面提到的相同代码来实现。

例如:

进入控制台，输入

```
$().jquery

//Or

$.fn.jquery

//Or

jQuery.fn.jquery 
```

Enter fullscreen mode Exit fullscreen mode

上面所有的代码会给你同样的结果。

## 如何检测/获取 jQuery UI 版本？

检测或检查 jQuery UI 的版本也很简单。不过 1.6 版本发布后略有变化。我们来看看代码:

```
/*Use this to check the version number in jQuery UI version 1.6 onwards*/
var jqueryUI_version = $.ui.version;

/*Use this to check the version number in jQuery UI prior to version 1.6*/
var jqueryUI_version = $.ui; 
```

Enter fullscreen mode Exit fullscreen mode

因此，请在此与其他读者分享您的经验。也请发微博或与你的朋友分享这篇文章。

帖子[如何检查 jQuery 版本？](https://www.moreonfew.com/how-to-check-jquery-version/)最早出现在 [MoreOnFew](https://www.moreonfew.com) 上。