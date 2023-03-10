# 美国有素食选择的餐厅

> 原文：<https://dev.to/pacocp/restaurants-with-vegetarian-options-in-usa>

几个月前，我发现了这个 [Kaggle 的数据集](https://www.kaggle.com/datafiniti/vegetarian-vegan-restaurants)，里面有美国供应素食和纯素食的餐馆。我想我可以用它做些有趣的事情。

我一直在拖延它，直到上周我向自己提出，我将使用它来学习如何实现一个 web 交互式地图。

首先，我用 [Plotly](https://plot.ly) 绘制数据，看看它是如何工作的。这很有趣，但并没有我想象的那么好，因为数据集真的很大。

然后我想，为什么不使用 JavaScript 库，然后使用 HTML 和 CSS 来创建网页呢？这就是我如何找到 [LeafletJS](http://leafletjs.com) 的，这是一个在地图上绘制地理位置数据的惊人的库。

由于数据集相当大，我还使用了一个名为[marker cluster](https://github.com/Leaflet/Leaflet.markercluster)的 LeafletJS 插件，它根据点的接近程度对它们进行聚类。

结果可以在这里查看[，所有代码可以在](http://pacocp.github.io/Restaurants_with_Vegetarian_Options_in_USA/) [Github 库](https://github.com/pacocp/Restaurants_with_Vegetarian_Options_in_USA)找到。

我希望这能帮助到一些人！

快乐编码:)

帕科