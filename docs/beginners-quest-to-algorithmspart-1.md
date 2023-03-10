# 算法初学者探索—第一部分

> 原文：<https://dev.to/benhaddish/beginners-quest-to-algorithmspart-1>

[![Alt Photo by Farzad Nazifi on Unsplash](img/b5f6ca2d3f6b2a1077436515c4612a9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_IBfEaQI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vw1sn06338zs2axar17p.jpeg) 
假设你对数组、变量、数据结构之类的编程概念大体了解一点；我将试着与你分享一些在我们日常寻求解决现实世界编程问题中最广泛应用的算法。我们的第一个算法将是著名的二分搜索法。希望这篇文章能帮助你学习算法的概念。

**二分搜索法**
二分搜索法算法在一个排序数组中找到一个项目。假设你在一个数组(排序数组)中搜索一个数字，通常不使用二分搜索法或任何算法，我们会搜索数组中的每一项来找到这个数字，如果数组很大，比如 100，000 项或更多，那么要找到这个数字就要花很多时间，特别是如果这个数字在数组的末尾找到，所以为了解决这个问题，我们将使用二分搜索法来找到这个数字。

以下是二分搜索法在幕后的工作方式:它接受排序后的数组并将数组分成两半。然后，它在数组的一个分割的一半上搜索给定的数字，如果我们要寻找的数字小于数组的前半部分，它将消除没有被搜索的数字的前半部分，并再次尝试将剩余的一半分成像第一半一样的其他一半，这个过程将继续，直到找到该数字。如果数组中存在该数字，二分搜索法将返回该数字的位置，如果找不到该数字，将返回 null。