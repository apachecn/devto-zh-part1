# 如何制作自动高度元素的动画

> 原文：<https://dev.to/marshallformula/how-to-animate-an-auto-height-element-1j3>

最初发布于[https://volumeintegration.com/animate-auto-height-element](https://volumeintegration.com/animate-auto-height-element/)

制作一个自动高度元素的动画看起来应该很简单，然而似乎[我不是唯一一个在这个特殊问题上挣扎的人](https://www.google.com/search?q=animation+auto+height+element)——这个问题通常是下面的一些变体:

*   我想让一些元素垂直展开和折叠。
*   该元素具有动态内容，因此扩展元素的高度是未知的/动态的。
*   我需要将元素的高度设置为—`auto`,以允许元素根据其内容改变高度。
*   CSS 不允许过渡到`auto`高度，所以它只是在展开/折叠时跳到这个高度

这是我想做的。

[![Showing auto-height expander](img/53f07fc60156627fa0ac11670d7b1835.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X837iD4W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://volumeintegration.com/wp-content/uploads/expandy.gif)

## 一些变通办法

如果你花点时间在网上逛逛，你可能会找到这个问题的几种可能的解决方案。

例如——有一个 **max-height** 的变通方法。在这个解决方案中，你基本上会转换`max-height`属性而不是`height`。技巧是将最终的 max-height 设置为比你认为元素将增长的值大得多的值。这将有效地动画显示元素内容的高度。这可能会让你觉得有点笨拙——这是有充分理由的。首先，你必须猜测遗嘱中最大的内容是什么。但是内容是动态的——所以很容易失控。此外，过渡将动画到完全指定的`max-height`。可见高度将在内容的高度停止——但是转换认为它需要一直增长到`max-height`。举个例子，如果你设置了一个`300ms`的过渡时间，即使视觉高度在此之前就停止了，也要花那么长的时间来制作完整的`max-height`动画。

其他解决方法包括隐藏视觉元素，而不是改变实际高度或使用 javascript 手动动画/隐藏元素等。，但是这些甚至比`max-height`解决方案更复杂，并且引入了一系列需要处理的新问题(至少对元素的可访问性造成了严重破坏)。

## 我的黑客解决方案

如果你是那种会偷看书末的人——那么你可以看看[我在 codepen](https://codepen.io/marshallformula/pen/qPvWBL) 上的工作解决方案。

它仍然使用 CSS 通过[转换属性](https://developer.mozilla.org/en-US/docs/Web/CSS/transition)来激活`height`属性，但是它也使用一些 JavaScript 来存储元素的一些状态。

这种解决方案并不适用于所有情况，但它很适合我的需求，但有一些限制:

*   您必须知道元素的初始默认高度——这意味着如果您不知道初始加载时 div 中会有什么内容——这可能不太好——但是如果您的元素有一组初始的已知内容，这应该可以像 champ 一样工作。
*   只有当元素处于展开状态时，才能在元素中添加或删除内容。如果在折叠时在 div 中添加/删除内容，那么您又要倒霉了。

假设您的需求满足这些要求——这应该很好。

该解决方案基本上是这样工作的:

1.  将元素的初始高度存储在某个变量中。我们暂时称之为`expandHeight`。
2.  当元素扩展时——您可以轻松地将高度从 0 转换到`expandHeight`。
3.  过渡完成后(根据您设置的[过渡持续时间](https://developer.mozilla.org/en-US/docs/Web/CSS/transition-duration)属性使用[设置超时](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout)，然后将元素的高度属性设置为`auto`
4.  根据需要向元素添加/移除内容
5.  当折叠时-
    1.  首先将元素的当前高度存储回`expandHeight`变量。
    2.  接下来，将元素的高度设置回一个固定值(您刚才存储在 expandHeight 中的值)。这是因为元素也不能从到`auto`高度转换*。它只能转换到固定高度或从固定高度转换。*
    3.  现在，您可以过渡回高度为 0。
6.  当您需要再次扩展时，只需从上面的第 2 步开始，并根据需要重复！

这就是它的全部内容，对我来说效果很好。一个警告是，您可能需要在另一个 [setTimeout](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout) 中加入步骤 5.3，并有一个非常小的延迟，以允许 DOM 有时间注册 height 属性已经从自动高度更改为固定高度。

这是我在 codepen 上的全功能例子。

敏锐的观察者可能会注意到，从这个解决方案中创建一个[高阶 ReactJS 组件](https://reactjs.org/docs/higher-order-components.html)并不需要太多的想象力，它会在内部存储自己的状态，因此您可以在任何地方轻松地重用它。

让我知道你的想法。更重要的是，让我知道你是否有更好的东西！干杯！

###### *专题照片由[克里斯蒂安·坎德尔](https://unsplash.com/@christiankaindl)拍摄 T3】*