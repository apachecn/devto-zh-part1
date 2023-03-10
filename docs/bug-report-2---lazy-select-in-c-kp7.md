# 错误报告 2——c#中的惰性选择

> 原文：<https://dev.to/horia141/bug-report-2---lazy-select-in-c-kp7>

这是我在工作中遇到的关于 bug 的系列文章的第二篇。第一个是关于`GETUTCDATE()`以及它如何在更长的查询中使事情变得复杂。

今天的 bug 是关于 [LINQ](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/getting-started-with-linq) 和它产生的懒惰`IEnumerables`。我花了太多时间的一个一次性脚本是这样工作的:

```
IEnumerable<Item> items = RawItems().Select(i => i.Transform());
foreach (var item in items)
    item.name = $"{item.name} #Modified";
UpdateItems(items); 
```

当然，这不是最优雅的代码。但我认为这足够完成它的工作了。原来有一个简单而微妙的错误正在发生。原来`Select`很懒，所以`items`更像是一个承诺，如果你在迭代它，你会从`RawItems`得到应用于每个元素的`Transform()`的结果。但是每次使用迭代器时，计算都要重做。

所以`foreach`循环实际上修改了迭代器一次调用产生的每个元素，但是所有的数据都被丢弃了。当`UpdateItems`使用`items`时，它获得了派生数据的新版本，没有任何修改。

解决办法很简单。要么将修改作为`Select`的一部分包含在`Transform`旁边。或者让`items`不懒惰，把它变成`List`或`Array`，为了简单起见，在我的实际设置中，我最终这么做了。所以代码现在只是:

```
IEnumerable<Item> items = RawItems().Select(i => i.Transform()).ToList();
foreach (var item in items)
    item.name = $"{item.name} #Modified";
UpdateItems(items); 
```

因为`foreach`循环和`UpdateItems`现在引用了一个列表，而不是某个东西上的迭代器，所以达到了期望的行为。