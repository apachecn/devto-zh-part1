# 在 CSS 中创建渐变不透明度？

> 原文：<https://dev.to/lecopsportif/create-a-gradient-opacity-in-css-29ni>

嘿伙计！如何在 CSS 中制作渐变不透明度？

我在 jsfiddle 上试了一下，给大家展示一下我的尝试。

```
#map {
height: 400px;
background: #405CB1;
-webkit-mask-image:-webkit-gradient(linear, left top, left bottom,from(rgba(64, 92, 177, 1)), to(rgba(64,92,177,0)))
} 
```

Enter fullscreen mode Exit fullscreen mode

[https://jsfiddle.net/qnr2079s/](https://jsfiddle.net/qnr2079s/)

渐变效果很好，但是没有考虑背景色。

谢谢大家的帮助！