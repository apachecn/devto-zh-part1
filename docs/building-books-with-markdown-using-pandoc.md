# 使用 pandoc 构建带降价功能的图书

> 原文：<https://dev.to/johnpaulada/building-books-with-markdown-using-pandoc>

> TL；DR:如果你想快速完成，安装 [pandoc](https://github.com/jgm/pandoc/releases/tag/1.19.2.1) 并使用我的 [markdown book 模板](https://github.com/johnpaulada/pandoc-markdown-book-template)。
> 
> 最初发布于[媒体](https://medium.com/programmers-developers/building-books-with-markdown-using-pandoc-f0d19df7b2ca)。

如果你是一个开发者或博客作者，你可能知道什么是 Markdown，如果你知道什么是 Markdown，你就知道这是描述你的内容的一个很好的方式。也许你想过用 Markdown 写本书。如果是的话，这是给你的。

如果你不知道 [Markdown](https://en.wikipedia.org/wiki/Markdown) 是什么，你可以查看以下链接:

*   [降价教程](http://www.markdowntutorial.com/)
*   [幽灵降价指南](https://help.ghost.org/hc/en-us/articles/224410728-Markdown-Guide)
*   [降价语法](https://learn.getgrav.org/content/markdown)
*   迪林杰

用 [**pandoc**](http://pandoc.org/) 建立一本书的 markdown 是相当容易的。Pandoc 称自己为*通用文档转换器*。有道理。在本教程中，我们将把 markdown 转换成 epub。你可以在 pandoc 中将 markdown 转换成其他格式，但我打算使用 epub，因为它们非常灵活。

## 安装 Pandoc

要安装 pandoc，请在 Github 的[发布版](https://github.com/jgm/pandoc/releases/tag/1.19.2.1)页面安装软件包。有 [Debian/Ubuntu](https://github.com/jgm/pandoc/releases/download/1.19.2.1/pandoc-1.19.2.1-1-amd64.deb) 、 [Windows](https://github.com/jgm/pandoc/releases/download/1.19.2.1/pandoc-1.19.2.1-windows.msi) 、 [Mac](https://github.com/jgm/pandoc/releases/download/1.19.2.1/pandoc-1.19.2.1-osx.pkg) 的包。

如果你在 macOS 上，你也可以通过运行:
来使用 Brew

```
brew install pandoc 
```

Enter fullscreen mode Exit fullscreen mode

如果你是硬核或者你的操作系统不受支持，你也可以从[源](https://github.com/jgm/pandoc/archive/1.19.2.1.tar.gz)构建。

## 创建简单的减价书

要创建一本简单的书，先创建一个 markdown 文件，然后将其输入 pandoc。例如，您可以尝试以下步骤:

1.  创建一个`chapter1.markdown`文件。
2.  打开您的终端/命令提示符。
3.  转到该文件所在的目录。
4.  在文件中添加一些降价信息。下面是它应该是什么样子的一个例子:

```
# Chapter 1
## Subtitle
This is your first markdown chapter! 
```

Enter fullscreen mode Exit fullscreen mode

### 建筑书

运行此命令创建图书:

```
pandoc -S -o book.epub content.markdown 
```

Enter fullscreen mode Exit fullscreen mode

您将得到一个`book.epub`作为输出。

## 创建元数据文件

当然，我们希望添加关于我们的书的信息，如标题、作者等。为此，我们将创建一个元数据文件。这是来自他们的[文档](http://pandoc.org/MANUAL.html#epub-metadata) :
的一个样本元数据文件

```
--------
title:
- type: main
  text: My Book
- type: subtitle
  text: An investigation of metadata
creator:
- role: author
  text: John Smith
- role: editor
  text: Sarah Jones
identifier:
- scheme: DOI
  text: doi:10.234234.234/33
publisher:  My Press
rights: © 2007 John Smith, CC BY-NC
... 
```

Enter fullscreen mode Exit fullscreen mode

此外，我们可以像这样添加一个样式表和封面图像的路径:

```
--------
title:
- type: main
  text: My Book
- type: subtitle
  text: An investigation of metadata
creator:
- role: author
  text: John Smith
- role: editor
  text: Sarah Jones
identifier:
- scheme: DOI
  text: doi:10.234234.234/33
publisher:  My Press
rights: © 2007 John Smith, CC BY-NC
stylesheet: epub.css
cover-image: cover.jpg
... 
```

Enter fullscreen mode Exit fullscreen mode

### 创建样式表

Pandoc epubs 有以下几种样式:

```
body { margin: 5%; text-align: justify; font-size: medium; }
code { font-family: monospace; }
h1 { text-align: left; }
h2 { text-align: left; }
h3 { text-align: left; }
h4 { text-align: left; }
h5 { text-align: left; }
h6 { text-align: left; }
h1.title { }
h2.author { }
h3.date { }
ol.toc { padding: 0; margin-left: 1em; }
ol.toc li { list-style-type: none; margin: 0; padding: 0; } 
```

Enter fullscreen mode Exit fullscreen mode

您可以编辑它来定制您的图书样式，并将其保存到元数据中指定的路径。

### 添加封面图片

要添加图像作为图书封面，您只需确保元数据中指定的路径指向该图像。

### 将元数据应用到图书

要应用创建的元数据，以及样式和封面图像，您只需将它作为输入包含进来，就像这样:

```
pandoc -S -o book.epub metadata.txt contents.markdown 
```

Enter fullscreen mode Exit fullscreen mode

### 嵌入自定义字体

要嵌入自定义字体，可以使用—-epub-embed-font 参数，并将字体的路径作为参数，如下:

```
pandoc -S **--epub-embed-font='fonts/*.ttf'** -o book.epub metadata.txt contents.markdown 
```

Enter fullscreen mode Exit fullscreen mode

### 添加目录

要添加目录，您可以使用— toc 开关，如下所示:

```
pandoc -S **--toc** --epub-embed-font='fonts/*.ttf' -o book.epub metadata.txt contents.markdown 
```

Enter fullscreen mode Exit fullscreen mode

## Epub 模板

为了让这个过程更容易，你可以试试我的 [Markdown Book Pandoc 模板](https://github.com/johnpaulada/pandoc-markdown-book-template)。要使用它，请遵循以下步骤:

1.  克隆或[下载](https://github.com/johnpaulada/pandoc-markdown-book-template/archive/master.zip)模板。
2.  打开你的控制台(终端，命令提示符等。)
3.  替换`metadata.txt`和`contents.markdown`的内容。
4.  更换`cover.jpg`。运行以下代码:

```
pandoc -S --toc --epub-embed-font='fonts/*.ttf' -o book.epub metadata.txt contents.markdown 
```

Enter fullscreen mode Exit fullscreen mode

这将生成`book.epub`。
瞧！你的书写完了！