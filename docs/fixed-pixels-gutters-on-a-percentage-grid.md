# 百分比网格上的固定像素间距

> 原文：<https://dev.to/trys/fixed-pixels-gutters-on-a-percentage-grid>

虽然网格不是最吸引人的主题，但它是网站的基本构件。我构建的每个项目都会用到这个固定装订线的片段，所以我想作为我的第一个开发发布分享它可能会很方便。

在过去，我倾向于在百分比宽度网格上使用百分比边距。这无疑使计算变得更加容易。两列(50/50)网格变成了`0/48%/4%/48%/0`网格。这个系统在孤立的情况下确实工作得很好，但是我发现当它嵌入到一个设计中时，问题就开始了。

考虑在每个部分之间使用`40px`垂直空间的设计。如果您对水平页边距使用百分比，那么当视窗关闭时，整齐一致的`40px`间距将会缩小。幸运的是，有更好的方法...

## 输入一致的边距

遗憾的是，不可能将两列(基于浮动的)网格写成`0/48%/40px/48%/0`，像这样混合百分比和像素只能在某些视口中工作。我们需要使用`calc`将`%`和`px`连接在一起。

这个概念是在每个元素上设置一个基于像素的`margin-left`(不包括每行的第一个)，然后在元素宽度上使用`calc`来说明像素间距。

宽度计算如下:

```
Element width in percentage - (No of gutters * gutter width / number of elements)px 
```

Enter fullscreen mode Exit fullscreen mode

举一个真实世界的例子，对于带有 30px 装订线的两列网格:

```
/* 50% - (1 * 30 / 2)px = 15px */
width: calc(50% - 15px); 
```

Enter fullscreen mode Exit fullscreen mode

或者 30px 装订线的三列网格:

```
/* 33.333% - (2 * 30 / 3)px = 20px */
width: calc(33.333% - 20px); 
```

Enter fullscreen mode Exit fullscreen mode

现在，当设计师向您展示一个带有 35px 装订线的五列网格时，您不需要测量每个元素并将其转换为百分比，只需将值传入计算:

```
/* 20% - (4 * 35 / 5)px = 28px */
width: calc(20% - 28px); 
```

Enter fullscreen mode Exit fullscreen mode

## 把一切都包起来

结合一点点`nth-child`优点，你就有了一个非常好的定制网格系统:

```
.grid > * {
    margin-bottom: 30px;
}

@media screen and (min-width: 30em) {
    .grid > * {
        margin-left: 30px;
        float: left;
    }
}

@media screen and (min-width: 30em) and (max-width: 45em) {
    .grid > * {
        width: calc(50% - 15px);
    }

    .grid > :nth-child(2n+1) {
        margin-left: 0;
        clear: left;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

##### 嘿！现在是 2017 年，我们现在有网格布局，谁还用`float`呢？

有效的观点。我仍然喜欢为仍在生产中使用的老式浏览器提供一个合理的后备。