# 缓存计算的痛苦必要性

> 原文：<https://dev.to/mortoray/the-agonizing-necessity-of-cached-calculations>

对缓存的语言支持糟透了。尽管缓存数据和计算的需求一直存在，但大多数语言都要求程序员完成所有的工作。没有办法跟踪依赖关系，没有办法知道缓存是否有效，甚至没有办法知道缓存是否有用。在这篇文章中，我将研究一些关键问题。

> 本文主要关注缓存计算，而不是数据缓存。有些问题和解决方案对两者都适用，但它们在性质上有些不同。

## 为什么缓存？

在 [Fuse](http://fusetools.com/) 中，我们做了大量的计算。我们需要[检查点击测试边界](https://mortoray.com/2017/03/08/a-game-of-whatd-they-press-hit-testing-in-a-2d3d-layout/)，[计算曲线边界](https://mortoray.com/2017/02/23/stuffing-curves-into-boxes-calculating-the-bounds/)，生成局部到世界的变换矩阵并计算渲染边界。我甚至会认为元素的绘制是一种计算。我确定我遗漏了什么；关键是我们有很多事情要做。

这些单独的计算本身都不是问题。然而，对于树中的每个节点，它们一起占用了大量的 CPU 时间。缓存提高了帧速率，降低了能耗。

> 我们还缓存继承的属性，如`IsVisible`和`IsEnabled`。我们这样做不是为了提高效率，而是为了在每个节点上这些值发生变化时生成事件。基于树的变量和事件本身是另一个令人惊愕的途径。

## 问题的结构

缓存的计算如下所示:

```
var cache_branch = merge( measure(leaf), measure(cone) ) 
```

Enter fullscreen mode Exit fullscreen mode

让我们暂时假设`measure`和`merge`都是纯函数(结果只取决于输入参数)。这意味着`cache_branch`一直有效，直到`leaf`或`cone`发生变化。但是我们怎么知道它们什么时候改变呢？

有两种基本方法。要么我们订阅一个`leaf_changed`事件:

```
defn update_cache_branch = -> {
    cache_branch = merge( measure(leaf), measure(cone) )
}

leaf_changed.subscribe( update_cache_branch )
cone_changed.subscribe( update_cache_branch ) 
```

Enter fullscreen mode Exit fullscreen mode

或者我们添加一个`leaf_version`并在每次计算时检查它:

```
get_cache_branch = -> {
    if leaf_version != cached_leaf_version or cone_version != cone_cached_version {
        cache_branch = merge( measure(leaf), measure(cone) )
        cached_leaf_version = leaf_version
        cached_cone_version = cone_version
    }
    return cache_branch
} 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这两种方法都要求更改`leaf`的人做更多的事情:要么发出更改的事件，要么增加版本。为了简单起见，我现在只提到基于事件的方法。

## 缺陷的来源

这个例子几乎是微不足道的，但我们已经可以看到即将到来的缺陷浪潮。要么是缓存忘记订阅事件，要么是生成器无法发出事件。

尽管如此，为了看到问题的真实范围，让我们把它变得更复杂。我们将用计算子节点渲染边界的`calc_bounds`替换`measure`，其中`leaf`和`cone`就是这样的节点(例如 UI 树中的元素)。

```
var cache_branch = merge( calc_bounds(leaf), calc_bounds(cone) ) 
```

Enter fullscreen mode Exit fullscreen mode

订阅`leaf_changed`不再有意义，因为我们实际上不知道`calc_bounds`使用了节点中的什么变量。我们也不想在这里开始订阅许多不同的事件，这容易出错且效率低下。

相反，我们将缓存的责任交给了节点本身:

```
var cache_branch = merge( leaf.bounds, cone.bounds ) 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在每个节点上订阅一个简单的`bounds_changed`事件。它保持了这个层次的简单，但是它把负担推低了一个层次。

每个节点需要缓存自己的`bounds`，并在更新时发出事件。这听起来可能很简单，但像 bounds 这样的东西涉及到很多变量:位置、大小、变换和可见性。这些变量中的任何一个发生变化都会使缓存失效。

## 事件昂贵

事件从来都不是免费的，这是个问题。缓存的渲染边界真的需要为所有感兴趣的变量订阅事件吗？现在考虑命中测试边界和布局也有缓存值。UI 树中的每个节点都需要订阅大量事件，以保持缓存有序。这些事件的管理和调度会增加更多的开销。

> 更糟糕的是，这些缓存值并不总是被使用。计算一个值，从而订阅事件是没有意义的，除非某个东西真的想要使用这个值。构建一个缓存系统实际上很容易，但最终性能会比没有缓存时更差！

在 Fuse 中，我们使用第三种方法来缓解这种情况。我们为键缓存硬编码了一些虚函数，而不是动态事件。像`InvalidateRenderBounds`和`OnRenderBoundsInvalidated`这样的东西。所有缓存的值都是延迟计算和缓存的:在失效后，直到实际使用时才再次计算。

这在效率上创造了奇迹，但却以复杂性为代价。

## 编译器帮助

无法避免对这些缓存的需求。不幸的是没有太多的语言支持来帮助。所有这些工作都让人感觉平淡而重复。我们使用的每个变量缓存最终都有相同的基本结构，要么订阅消息，要么检查版本。每次我们设置一个变量，我们都有相同的基本事件发射或递增版本。

这一切都像是编译器以一种高效而安全的方式为我们做的事情。我还不清楚语法应该是什么。这不是真的，理想的语法应该是:

```
defn get_cache_branch = -> {
    return lazy_cache_calc( merge( measure(leaf), measure(cone) ) )
} 
```

Enter fullscreen mode Exit fullscreen mode

其中`lazy_cache_calc`设置缓存所需的一切:事件和订阅，或者版本和检查，在完整的依赖树上。虽然看起来我可能要求的有点多，但这本质上是反应式编程所做的。它通常只保留给更高级的编程。

我将不得不进一步调查。