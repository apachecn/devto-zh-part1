# 响应块和垂直对齐

> 原文：<https://dev.to/samturrell/responsive-blocks-and-vertical-alignment-3jb>

今天我将向你展示我最喜欢的两个 CSS“技巧”响应高度块和垂直对齐。这两者通常是紧密相连的，通常是一些中间有文本的网格块。我相信你以前也遇到过这种情况。

## 响应式积木

首先，有反应的积木。我们都用过响应式宽度栏，但你可能从未用过响应式高度栏。如果您将`height: 50%;`应用于一个元素，您将看不到任何结果。与宽度不同，高度不能设置为百分比，因为块容器元素没有高度，而它们默认为 100%宽度。

然而，我们可以相对于宽度来确定高度。为此，我们将利用`padding`规则。如果你将`padding-bottom: 100%;`的规则应用于一个 div，你将得到一个完美的正方形。然而，一整页的正方形并没有什么帮助，不是吗？我们想要一个正方形网格。你可能认为在这个 div 上设置一个宽度并观察它的缩放就可以很容易地实现，然而事实并非如此。填充实际上是相对于**父**元素，*而不是*应用了规则的元素。

因此，为了使 box 更小，响应性更大，我们只需要一个具有宽度值的容器元素。这通常没问题，因为它可能在一个网格列中。现在你只要告诉孩子它相对于父母应该有多大，你就有了一个完美的反应高度方块。

```
<div style="margin: 0 -10px;" class="clearfix">
    <div style="width: 25%; float: left; padding: 0 10px;">
        <div class="primary" style="padding-bottom: 100%;"></div>
    </div>
    ...
</div> 
```

当然，你可以通过遵循模式和调整填充百分比来制作响应矩形。

## 垂直对齐

啊，垂直对齐，前端开发人员的祸根。然而，这并不是必须的。根据使用情况，您可以遵循一个简单的模式来实现垂直对齐，这个模式非常适合上面的 responsive blocks 提示。

你可能会不寒而栗，但我们将使用表格！嗯，不完全是桌子。假桌子。表格具有强大的能力，能够利用`vertical-align` CSS 规则来对齐内容，通过使用`display: table;`和`display: table-cell;`，我们实际上可以利用标准 div 的这一功能。

您所需要的只是一个包含`display: table;`的 div，以及其中包含`display: table-cell;`和`vertical-align: middle;`的 div。只要给父 div 一个高度，就可以完美地输入内容。

```
<div class="playground">
    <div class="primary" style="display: table; height: 100px; max-width: 100%; width: 100%; text-align: center;">
        <div style="display: table-cell; vertical-align: middle; padding: 15px;">
            Look at me being all vertically aligned!
        </div>
    </div>
</div> 
```

有些人可能会认为 flexbox 可以用少得多的代码行解决这个问题，但是每个浏览器都支持 table-cell 以获得无忧的开发体验。

## 祸不单行

结合以上就简单了。但是，如果您试图将您的伪表放入响应块中，您将会得到一个不再可靠响应的块。这是因为填充技巧的高度与您的桌子的高度相结合。相反，我们需要在响应块中添加另一个容器块。

只需在带有填充的块上设置`position: relative;`,然后用`position: absolute; top: 0; right: 0; bottom: 0; left: 0;`在其中添加一个新的 div，这将使这个 div 与父 div 大小相同，从而允许您向块中添加内容。

这个块现在也有一个固定的高度，所以你现在可以为你的内容使用一个百分比高度值。只需将垂直对齐的块添加到这个 div 中，并赋予它 100%的高度和宽度，您将在块中获得完美对齐的内容。

查看[的原始帖子](https://samturrell.github.io/2018/01/11/responsive-blocks-and-vertical-alignment/)来看看这些例子。