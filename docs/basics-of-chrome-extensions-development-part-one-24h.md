# Chrome 扩展开发基础第一部分

> 原文：<https://dev.to/aurelkurtula/basics-of-chrome-extensions-development-part-one-24h>

在这个由两部分组成的系列中，我将探索如何开发 chrome 扩展。让本教程继续下去的最简单的方法是说我们可以用一个扩展做两件事。我们可以让它改变我们访问的网站的行为，或者让它成为一个独立的小型网络应用。

浏览我安装的扩展，两个解释我的意思是，广告拦截器和 1passport。虽然它们的开发远远超出了本教程的范围，但广告拦截器基本上改变了我们访问的实际网站的行为(通过删除广告)，1passport 只是一个弹出的小窗口，它可以是一个独立的网站。

我们今天要构建的扩展将阻止那些可能让你进步的网站。重点不在于实际制作一个生产就绪的扩展，而是开玩笑地强调如何创建扩展。

## 设置

每个扩展名都需要一个`manifest.json`文件。现在可以这么简单:

```
{
    "manifest_version": 2,
    "name": "SomeName",
    "description": "To stop you from waisting time",
    "version": "1.0",
} 
```

Enter fullscreen mode Exit fullscreen mode

所有这些都是通用的，它给用户提供了关于我们扩展的信息。所以这就像`package.json`在 wordpress 主题中节点应用程序，甚至是主样式表顶部的元数据。

`manifest_version`字段是必需的，由 Chrome 内部使用。以前需要在`1`设置，现在需要在`2`设置。如文档中所述，从 Chrome 18 开始，版本 1 已被弃用。这当然意味着如果你的目标受众不能升级，你会使用`1`。

### 安装分机

虽然我们还没有创建任何东西，`manifest.json`文件给了 chrome 足够的信息来识别它是一个扩展。

在您的浏览器中，导航到`chrome://extensions/`，然后启用`Developer mode`，最后选择`Load unpacked extension`，这将使您能够上传上述清单文件所在的文件夹。

最终结果是这样的:

[![](img/efac296af8737f6f3fffca80fdd0e5e5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_Fy5f6MP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/loumfqe4l4t4937qoqnn.png)

请注意，版本号和描述与清单文件相对应。还要注意，您可以添加自己的图标，我在这里没有指定，这可以通过向 JSON 文件添加一个`icon`字段来完成

```
{
    ...
    "icons": { 
        "16": "icon16.png",
        "48": "icon48.png",
        "128": "icon128.png" 
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

图标大小为 16 的会出现在扩展栏上，48 的会出现在扩展页面上(取代上面的占位符)，至于 128 的，可能在扩展商店里。

从现在开始，当我们在扩展的基础上构建时，当你想测试你的进展时，你只需要重新加载扩展(见重新加载链接的截图)。

## 让我们开始建造这个浓缩器吧

我们打算做的每件事都必须在`manifest.js`中指定，所以只需通过添加以下内容来编辑它。

```
"content_scripts": [
    {
        "matches": [
            "*://*.facebook.com/*",
            "*://*.twitter.com/*"
        ],
        "js": ["myscripts.js"]
    }
] 
```

Enter fullscreen mode Exit fullscreen mode

我们告诉 chrome，这个扩展应该只在访问 facebook 和 twitter 页面上的任何页面时运行。然后应该运行`myscripts.js`。

现在我们只需要创建`myscripts.js`文件，并在其中添加我们想要的任何内容。您可以在浏览器控制台中做的任何事情都可以放在这里。对于 twitter 来说，一个有趣而无用的自我助推器可能是这样的:

```
document.querySelectorAll('.ProfileCardStats-stat')[2]
    .querySelector('.ProfileCardStats-statValue').innerHTML = '34K' 
```

Enter fullscreen mode Exit fullscreen mode

当你访问 twitter 时，你会看到这个:

[![](img/8df08a5d0985c2b73c64a8b7e5cc59b2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--PYMUl3aN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/68wf4j57bc1s9tb5hkdv.png)

你会说“嗯，一夜成名就在昨天”(你甚至可以添加一个计时器，这个数字每周都会增加)。

不管怎样，去掉它，这就是我们在`myscripts.js`文件中想要的实际代码:

```
document.body.innerHTML = `
<!doctype html>
<html>
  <body>
<div class="wrap"
style="background-image: url('${chrome.runtime.getURL("danger.jpeg")}')">
        <h1>Get back to work you little ...</h1>
</div>
  </body>
</html>` 
```

Enter fullscreen mode Exit fullscreen mode

就像你日常的 JavaScript 一样。当你访问 facebook 或 twitter 时，你会看到一个空白页面，上面写着“回去工作吧，你这个小……”(填空)。

### 页面样式

该页面现在缺少样式。这是我想要的最终结果:

[![](img/1454213cc44bb6125f04df310a9b943b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kDlO8HAk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vpyosjty7afp82clumay.png)

(图片取自 [unsplash](http://unsplash.com) )

为此，我们需要一个样式表和一个图像。和 JavaScript 文件一样，我们必须编辑`manifest.json`来通知 chrome 它应该访问更多的文件。

```
"content_scripts": [
    {
        ...
        "css": ["mystyles.css"]
    }
],
"web_accessible_resources": [
    "danger.jpeg"
] 
```

Enter fullscreen mode Exit fullscreen mode

这样，`mystyles.css`就在页面加载后运行，我们想要访问的图像需要在`web_accessible_resources`中指定。当然，我们可以使用通配符，比如`*.jpg`，任何东西都可以添加进去——自定义字体和所有这些好东西。

在这里打印 css 是没有意义的，你可以在 repo 中看到它，但是基本上我们在`mystyles.css`中写的任何 css 都被应用到页面上

然而，我们不能使用 css 文件中的图像，但是我们可以通过 js 访问它，如下所示:

```
chrome.runtime.getURL("danger.jpeg") 
```

Enter fullscreen mode Exit fullscreen mode

因此，我简单地编辑了 JavaScript 代码，应用了内联`background-image`样式:

```
<div class="flex-container"

    style="background-image: url('${chrome.runtime.getURL("danger.jpeg")}')"> 
```

Enter fullscreen mode Exit fullscreen mode

## 
 [T3】
结论](#conclusion) 

这就是了。这是开发 Chrome 扩展的第一部分。请记住，在开发和测试扩展时，您需要重新加载扩展以反映更改。

在[第二部分](https://dev.to/aurelkurtula/basics-of-chrome-extensions-development-part-two-82p)中，我们将创建一个扩展，在这个扩展中，必须单击一个图标才能打开一个弹出窗口。

你可以在 [github](https://github.com/aurelkurtula/basic-chrome-extensions) 获取代码