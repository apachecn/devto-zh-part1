# 创建一个 chrome 扩展，使用 jQuery 操作页面的 DOM

> 原文：<https://dev.to/designer023/creating-a-chrome-extension-that-uses-jquery-to-manipulate-the-dom-of-a-page-53om>

有大量关于如何进行 chome 扩展的教程和指南，也有大量关于 DOM 的 jQuery 操作。也有一些关于在 chome 扩展中使用 jQuery 的，但是只针对弹出窗口。似乎缺少使用 jQuery 在页面上实际做一些事情。这是我花了一段时间才弄明白的事情，我也搜索了一下才弄明白，所以现在是分享的时候了。

为扩展文件创建一个文件夹。最好用您的扩展名来命名它，这样您以后就可以找到它了。

在纯文本编辑器中创建 3 个空白文档。您可以将文件保存为`manifest.json`、`background.js`和`background.html`。

使用你最喜欢的图像编辑软件创建一个 png 图标，背景透明(目前 20px x 20px 应该可以了)。Chome 应该为它显示的不同区域缩放图标。稍后我们可以创建不同分辨率的图标，并告诉 chrome 使用它们。

[获取 jQuery](http://docs.jquery.com/Downloading_jQuery) 的副本，并将其添加到文件夹中。

在您的文件夹中，现在应该有以下文件:

manifest . JSON
T1】background.html
T3】background . js
T5】icon.png
T7】jquery . min . js

让我们看一下每个文件将包含什么，然后是什么。

**manifest.json**

```
{ 
    "name": "jQuery DOM", 
    "version": "1", 
    "description": "Manipulate the DOM when the page is done loading", 
    "background_page": "background.html", 
    "browser_action": { 
        "name": "Manipulate DOM", 
        "icons": ["icon.png"], 
        "default_icon": "icon.png" 
    }, 
    "content_scripts": [ 
        { 
            "js": ["jquery.min.js", "background.js"],
             "matches": ["http://*/*", "https://*/*"]
         }
     ] 
} 
```

Enter fullscreen mode Exit fullscreen mode

manifest.json 的重要部分是:

```
 "content_scripts": [ 
        { 
            "js": ["jquery.min.js", "background.js"], 
            "matches": ["http://*/*", "https://*/*"] 
        }
    ] 
```

Enter fullscreen mode Exit fullscreen mode

js 告诉扩展加载 jquery，然后加载您的定制 js 文件(`background.js`)。这些匹配让您可以控制扩展可以在哪些页面上运行。在这种情况下，它都是 http 和 https 页面。你可以把它限制在一页之内。

**icon.png**

它只是一个 png 图像。

**jquery.min.js**

您需要使用的 jQuery 的简化版本。不一定要缩小，但我们也可以。

**background.html**

并不总是需要，但是我已经在这个例子中包括了它。你可以在谷歌的扩展页面上了解更多信息。因为我什么也没做，所以我的只是一个空文档。

```
<!doctype html> 
    <html> 
    <head> 
        Background Page 
    </head>
    <body>
    </body> 
</html> 
```

Enter fullscreen mode Exit fullscreen mode

**background.js**

这是我们使用 jQuery 的地方。不需要文档就绪侦听器，因为扩展实际上是在页面之后加载的，所以页面加载不受影响。

```
$("body").append('Test'); 
```

Enter fullscreen mode Exit fullscreen mode

一旦任何页面加载，它将附加一个 pargraph 与'测试'作为文本。很明显，我们可以做更多有趣的事情，但那是你和你的 jQuery 魔术要做的。

## 给 chrome 添加扩展

现在我们有了所有的文件，它们都有我们需要添加到 chrome 的正确内容。

1.  点击扳手图标并选择`Tools > Extensions`，调出扩展管理页面
2.  勾选开发者模式复选框
3.  单击“加载解压缩的扩展”，选择您的文件夹，然后单击“确定”。如果所有文件都有效并且命名正确，图标应该出现在带有其他扩展名的地址栏旁边。您所做的任何更改都需要通过点击插件扩展页面中的**重新加载**来重新加载。

如果你现在刷新网页，它应该附加一个“测试”的段落

下载整个示例扩展，看看它是如何工作的。

你也可以[从 github](https://github.com/Designer023/chromesnowext) 克隆 repo。

**编辑:**

[创建 chrome 扩展——更新](https://dev.to/articles/creating-a-chrome-extension-an-update/)现在这篇文章的后续是一个更新的指南，解决了这里已经评论过的问题。