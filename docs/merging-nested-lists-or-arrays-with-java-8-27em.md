# 用 Java 8 合并嵌套列表或数组

> 原文：<https://dev.to/bmuskalla/merging-nested-lists-or-arrays-with-java-8-27em>

当使用分页访问第三方 API 时，我们往往会一遍又一遍地看到相同的模式。通常，一个响应(表示为 POJO)看起来像这样:

```
class Result {
  public List<Item> getItems() {
    ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

无论是来自第三方服务还是您自己的 API，在检索 API 调用的所有结果后，我们最终会得到类似于`List<Result>`的东西。太好了。我们并不真正关心回答本身，我们实际上对回答的所有项目感兴趣。假设我们想要所有的项目，过滤掉其中的一些，并将它们转换成一个`TransformedItem`。人们通常开始写类似下面的东西:

```
List<Result> results = ...
List<TransformedItem> newItems = results.stream()
     .map(result -> result.getItems())
     .filter(item -> item.isValid())
     .map(TransformedItem::new)
     .collect(toList()); 
```

Enter fullscreen mode Exit fullscreen mode

不对不对，这个都不编译。问题是第一张地图没有返回一个`Stream<Item>`，而是返回了一个`Stream<List<Item>>`。为了合并/展平所有嵌套列表，你可以使用 [Stream#flatMap](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html#flatMap-java.util.function.Function-) 。区别很简单。`#map`允许你将流中的一个元素转换成另一个元素。另一方面，`#flatMap`允许您将单个元素转换成多个(或者没有)元素。

```
List<Result> results = ...
List<TransformedItem> newItems = results.stream()
     .map(result -> result.getItems())
     .flatMap(List::stream)
     .map(TransformedItem::new)
     .collect(toList()); 
```

Enter fullscreen mode Exit fullscreen mode

万一你使用的第三方 API 返回了一些难看的东西，比如`List<Item[]>`，你可以使用相同的模式，只需选择相应的 flatMap 函数。

```
class QueryResponse {
  public Item[] getItems() {
    ...
  }
}

...

List<TransformedItem> newItems = results.stream()
     .map(result -> result.getItems())
     .flatMap(Arrays::stream)
     .map(TransformedItem::new)
     .collect(toList()); 
```

Enter fullscreen mode Exit fullscreen mode

和`#flatMap`玩得开心，让我在 Twitter 上知道[你是如何在你的场景中使用`#flatMap`的。在马丁·福勒](https://twitter.com/bmuskalla)[收集管道](http://martinfowler.com/articles/collection-pipeline/)中可以找到关于如何组成流和流背后的一些概念的很好的解释。