# SASS -入门

> 原文：<https://dev.to/sarah_chima/sass--getting-started-bbc>

你有没有想过什么是 SASS？或者也许你知道它是什么，但没有真正花时间去学习和使用它？或者你可能只是想提醒自己已经知道的事情。无论如何，这篇文章是给你的。

什么是 SASS？

SASS 是一个 CSS 预处理器。它是 CSS 的扩展，为 CSS 增添了力量和优雅。它允许您使用变量、嵌套规则、混合、内联导入和其他很酷的东西，所有这些都具有完全兼容 CSS 的语法。所有这些都有助于减少 CSS 的重复，节省时间。它有助于保持大型样式表的良好组织和可维护性。

**萨斯和 SASS 有什么不同？**
萨斯基本上是老版本的 SCSS。它的语法与传统的 CSS 有很大的不同，并且没有被很多人接受。例如，它使用缩进而不是大括号，并且不需要分号。在其版本 3 中，Sass 将其主要语法更改为`.scss`。SCSS 是 CSS 的超集，基本上和 SCSS 写的一模一样。所以我们可以像 CSS 一样编写 SCSS，同时还能获得 Sass 的最佳特性。
您仍然可以使用 Sass 语法，但是在本文和后续文章中，我们将使用 SCSS。

**使用 SASS**
要开始使用 SASS，您需要为您的项目进行设置。有几种方法可以做到这一点，但有两种选择:

1.  使用 Ruby 安装，可以在 SASS 网站上看到。
2.  安装[节点-萨斯](https://www.npmjs.com/package/node-sass)。如果您还没有在本地机器上安装节点，请安装节点。然后`cd`进入你想用 sass 运行的项目:

```
 npm install node-sass 
```

Enter fullscreen mode Exit fullscreen mode

**编译 SASS** 安装后，完成此操作的基本方法是在命令提示符下运行以下代码。

```
 sass input.scss output.css 
```

Enter fullscreen mode Exit fullscreen mode

其中`input.scss`是输入文件，而`output.css`是您希望将 sass 文件编译到的目标文件。这两个文件都可以重命名为您想要的名称，如果它们在不同的文件夹中，您需要指定路径。

如果您遵循任何安装过程，这里有一个快速的方法来测试我们安装了什么。在安装了 Sass 的项目中创建一个名为 test.scss 的文件。将以下代码添加到该文件中。如果你不懂代码也不用担心，只是把它当作一个测试。你很快就会明白的。

```
 $primaryColor : blue;

    .test {
        color : $primaryColor;
    } 
```

Enter fullscreen mode Exit fullscreen mode

然后在命令提示符下运行

```
 sass test.scss output.css 
```

Enter fullscreen mode Exit fullscreen mode

这将创建两个文件，`output.css`和`output.css.map`。在`output.css`中你应该会看到这个:

```
 .test {
        color: blue; } 
```

Enter fullscreen mode Exit fullscreen mode

如果你看到了，恭喜你，你已经设置好了。

你每次写新的 sass 时都运行过那行代码吗？除非你想。您可以改为观看文件

```
 sass --watch input.scss:output.css 
```

Enter fullscreen mode Exit fullscreen mode

这样做的目的是每当发生任何变化时，自动编译您在`input.scss`到`output.css`中编写的每个 sass。你也可以看目录。

```
 sass --watch app/sass:public/css 
```

Enter fullscreen mode Exit fullscreen mode

这是我写的关于 SASS 的系列文章的第一篇。以下是我写的关于 Sass 的其他文章的链接:

[变量](https://dev.to/sarah_chima/sass-variables-2pb)
[嵌套](https://dev.to/sarah_chima/nesting-in-sass-bme)
[偏旁](https://dev.to/sarah_chima/using-sass-partials-7mh)
[导入](https://dev.to/sarah_chima/using-sass-partials-7mh)
[Mixins](https://dev.to/sarah_chima/sass-mixins-19a)
[继承](https://dev.to/sarah_chima/the-goodness-of-sass-inheritance-5hm)
[运算符](https://dev.to/sarah_chima/sass-operators-56f)
[控制指令](https://dev.to/sarah_chima/sass-control-directives-6hk)

有什么问题或补充吗？留下评论。

感谢阅读。:)