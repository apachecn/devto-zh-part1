# calc 的乐趣()

> 原文：<https://dev.to/designer023/the-joy-of-calc-110n>

是一个现代的 css 函数，允许你在一个 css 文件中做基本的数学运算。您可以使用它来非常轻松地制作半流体布局。这意味着你可以有一个固定宽度的列(或更多)和其余的流体布局。它不仅仅停留在列上，但这是讨论起来最简单快捷的例子。

calc 的设置非常简单。如果我们以一个 2 列布局为例，侧列总是有 200 像素的宽度，主列将占用其余部分，因此 100%少 200 像素！

```
/* Fixed columns */ 
.side { width: calc (200px); } 
.main { width: calc (100% - 200px); } 
```

Enter fullscreen mode Exit fullscreen mode

唯一的小问题是[支持](http://caniuse.com/#search=calc)，它扩展到几乎所有的浏览器，除了 Android 和 Opera Mini 目前不支持，黑莓有部分支持。IE9+都全力支持。

所以负责任地使用它只需要一些简单的后援。有一些妥协，但这些是相同的任何前计算流体布局。

```
/* Fixed columns with fallback */ 
.side { width: 20%; width: calc (200px); } 
.main { width: 80%; width: calc (100% - 200px); } 
```

Enter fullscreen mode Exit fullscreen mode

现在，任何不理解 calc 的浏览器都将使用百分比值，而更聪明的浏览器将使用新的 calc 值覆盖百分比。

上面的例子是为了创建一个全新的设计，然而更新一个现有的布局就像在下一行添加 calc()值一样简单。

calc()的其他用途包括在流体布局上设置固定的间距，在容器中设置固定高度的标题和滚动内容，设置背景图像距离右侧或底部一定数量的像素。

```
/* Gutters */ 
.column { width: calc(25% - 10px); margin-right: 10px; } 
/* Background image at bottom right */ 
.container { background-position: calc(100% - 10px) calc(100% - 10px); } 
/* Fixed headers */ 
.container { height: 300px; }
.container-header { height: 50px; } 
.container-content { height: calc(100% - 50px); } 
```

Enter fullscreen mode Exit fullscreen mode

我确信使用`calc()`还有更有创造性的用途，但是即使是上面所说的，每天都非常有用。我确实开始尝试使用 n-child 和`calc()`,但是他们不想玩球！

```
/* Code that will never work! */ 
.steps-for-fun:nth-child(n) { width: calc(n * 10px;);
 /* doubt this will ever work! */ 
} 
```

Enter fullscreen mode Exit fullscreen mode