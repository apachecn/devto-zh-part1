# Syncfusion 图表中的 TrackballLabelTemplate 仅适用于列表

> 原文：<https://dev.to/jonstodle/trackballlabeltemplate-in-syncfusion-chart-only-works-with-a-list-3ni9>

在花了太多时间试图解决这个问题后，我不得不把它写下来，以帮助任何不幸的人遇到这种未记录的行为。(注意我是怎么把所有重要的关键词都放在标题里的？)

而在 Xamarin 中设置集合视图的`ItemsSource`时，通常建议提供一个`IList<T>`。表单或其他 XAML 平台，这不是必需的。你也可以提供一个或`IEnumerable<T>`,事情就会好起来。

传递列表的好处是系统可以随机访问条目。换句话说，它可以使用`mylist[42]`语法。这比试图访问一个`IEnumerable<T>`中的随机项目要有效得多。

你可能会想“但是在`IEnumerable<T>`上有`ElementAt()`”。是的，但这基本上只是在访问所需项目之前跳过`n - 1`项目。`myEnumerable.ElementAt(42)`基本上就是这个:`myEnumerable.Skip(42 - 1).First()`。

在 [Xamarin。Forms documentation 它实际上明确地告诉你，出于性能原因，如果可以的话，提供一个列表](https://developer.xamarin.com/guides/xamarin-forms/user-interface/listview/performance/#Improving_ListView_Performance)。

###### 回 *SfChart* by Syncfusion:

显然在 *SfChart* 中有一个限制，当谈到`IEnumerable<T>`作为`ItemsSource` : **如果你为你的图表指定一个自定义`TrackballLabelTemplate`，如果你不使用`IList<T>`它将被忽略。**

这让我纠结了好一会儿。

我通常在绑定到`ItemsSource`时提供一个列表，但是在这个实例中，我在视图代码隐藏中使用了`Where()`来过滤掉一些不需要的项目。自从`Where()`返回后，一只`IEnumerable<T>`的东西就没了作用。在`Where()`条款后加上`ToList()`就解决了所有问题。

这浪费的时间比我想承认的要多...

如果你通过谷歌偶然发现了这篇文章；不客气:)

编码快乐！