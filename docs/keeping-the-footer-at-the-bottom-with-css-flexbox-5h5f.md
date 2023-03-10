# 使用 CSS Flexbox 将页脚保持在底部

> 原文：<https://dev.to/domysee/keeping-the-footer-at-the-bottom-with-css-flexbox-5h5f>

刚才我看了帖子[用 CSS-Grid](https://dev.to/niorad/keeping-the-footer-at-the-bottom-with-css-grid-15mf) 把页脚留在底部。
在阅读的时候，我莫名其妙地得到这样的印象，仅仅用 flexbox 做同样的事情是困难的。不是的。

这不是以任何方式贬低引用的帖子，它解释得很好，在我看来比 flexbox 更优雅。我只是想解释一下，使用 flexbox 和使用 grid 一样简单，所以如果出于某种原因，您不能使用 grid，您也可以使用 flexbox 来实现它。

我将使用与参考文章中相同的 HTML:

```
<html>
    <body>
        <article>
            <header>
            </header>
            <main>
            </main>
            <footer>
            </footer>
        </article>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

这里的 CSS 将页脚放在底部，它属于:

```
html, body {
    width: 100%;
    height: 100%;
}

article {
    min-height: 100%;
    display: flex;
    flex-direction: column;
    align-items: stretch;
}

main {
    flex-grow: 1;
}

header, main, footer {
    flex-shrink: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

在容器上，我们设置 flexbox 来对齐一列中的内容。元素应该伸展，以便它们跨越整个宽度，而不仅仅是它们的内容所占据的宽度。

在 main 上设置`flex-grow: 1`使其增长以填充可用空间。这将页脚放在底部，因为`main`占据了中间的所有空间。

`flex-shrink: 0`的用法可能不太明显，而且经常被遗忘。至少我忘记的次数比我想承认的要多。

默认情况下，`flex-shrink`设置为`1`。如果没有足够的空间，这将使项目收缩，如果内容大于屏幕，就会发生这种情况。结果可能看起来很奇怪，例如，按钮比它包含的文本小。将它设置为`0`会停止这种行为。

就是这样。这就是使用 flexbox 将页脚定位在底部所需要知道的一切。:)

* * *

在 Twitter 上关注我，了解更多我的想法、文章、项目和工作。