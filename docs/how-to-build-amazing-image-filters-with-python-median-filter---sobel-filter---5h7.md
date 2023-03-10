# 如何用 Python 构建令人惊叹的图像滤波器——中值滤波器📷，索贝尔过滤器⚫️ ⚪️

> 原文：<https://dev.to/enzoftware/how-to-build-amazing-image-filters-with-python-median-filter---sobel-filter---5h7>

# 如何用 Python 构建惊艳的图像滤镜——中值滤镜📷，索贝尔过滤器⚫️ ⚪️

如今，我开始使用一种新的编程语言:Python🐍。我很高兴分享我对这个新课题的一些知识，这就是图像处理。
[![digital image processing](img/7e803256dfddc4862de624fae351c316.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--Yr8xiqjl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A6N1clEI8EInQ5I8MWaOUSQ.png)

## 什么是数字图像处理？

这是一组技术，其目标是通过特定应用从源图像中获得结果，改进某些特征，以便从图像中提取信息。好了，在简单介绍了图像处理的含义后，让我们继续我将在本文中展示的滤镜。

## 中值滤波

[![Median filter](img/7aa06650a8e03fa589cdc4275b00bf74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wt8y6hP6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AVyzJxDrLrC-OqkV5LHhjQQ.gif) 
摘自【http://artemhlezin.com/2016/09/04/median.html】的
这种滤波器是用来消除图像的‘噪声’，主要是椒盐噪声。
除了图中的那个，没有太多理论。过滤器是这样工作的:获取掩码中的所有值，对它们进行排序，然后将平均值分配给坐标。
这是一个带有椒盐噪声的图像:Python 中的
[![](img/65a78040c56e01f519dc81078524b4d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--phun33qK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AUmRXiE--hLWK1g0MYeLBXQ.png) 
🐍过滤器是这样工作的，进入查看结果: