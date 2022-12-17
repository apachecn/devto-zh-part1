# 创建 chrome 扩展-更新

> 原文：<https://dev.to/designer023/creating-a-chrome-extension-an-update-3p7e>

自从我写了上一篇关于[创建 Chrome 扩展](https://dev.to/articles/creating-a-chrome-extension-that-uses-jquery-to-manipulate-the-dom-of-a-page/)的文章以来，扩展的构建方式发生了一些变化——主要是清单版本已经更改为版本 2。最初的帖子仍然很好，但需要做一些调整。这篇文章主要是对原文的更新，所以我不会过多地重复旧的内容，我会快速浏览基本内容。

*   为你的插件创建一个文件夹——以你的扩展名命名，这样你以后就可以找到它了！
*   在文本编辑器中创建 4 个文档。
*   将它们另存为 manifest.json、background.js、background.html 和 customStyles.css
*   使用您最喜欢的图像编辑器创建一个 20px x 20px 的 png 文件！
*   [获取 jQuery](http://docs.jquery.com/Downloading_jQuery) 的副本。并将其添加到文件夹中。

### 文件

#### [T1】manifest . JSON](#manifestjson)

```
{ 
  "name": "plugin name", 
  "version": "0", 
  "description": "What do I do as an extension", 
  "background": {
    "page": "background.html"
  }, 
  "manifest_version": 2,
  "browser_action": {
    "name": "Manipulate DOM", 
    "icons": ["icon.png"],
    "default_icon": "icon.png"
   }, 
   "content_scripts": [ 
      { 
        "js": ["jquery-2.0.2.min.js", "background.js"], 
        "css": ["customStyles.css"], 
        "matches": ["http://*/*", "https://*/*"]
      }
   ]
} 
```

Enter fullscreen mode Exit fullscreen mode

#### jquery-2 . 0 . 2min . js

jQuery 的副本。还不如缩小。

#### background.html

并不总是需要，但我们涵盖了基本的，所以在这里！

#### [T1】background . js](#backgroundjs)

一旦页面加载完毕，这个文件中的 js 就会运行。这发生在 DOM 加载之后，所以不需要事件监听器。

### 将其添加到 Chrome 中

现在我们需要做的就是给 chrome 添加扩展。

*   工具>扩展
*   勾选“开发者更多”复选框
*   单击“加载解压缩的扩展”并选择您的文件夹。文件中的任何错误都会显示出来。这不包括 JS 错误，只是像丢失的文件或格式错误的 manifest.json

当你更新你的扩展时，只需点击“重新加载”按钮，然后刷新你正在应用扩展的网页。

### TLDR:

我在 GitHub 上创建了一个 [Chrome 扩展启动包](https://github.com/Designer023/Chrome-Extension-Starting-Bundle)，让你马上开始。看一看，让我知道你的想法。