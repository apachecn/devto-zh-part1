# 解释如何读/写贝塞尔曲线，就像我五岁一样

> 原文：<https://dev.to/isaacdlyman/explain-how-to-readwrite-bezier-curves-like-im-five>

CSS 有`transition-timing-function: cubic-bezier(w, x, y, z)`。SVG 支持贝塞尔曲线(它们渲染速度非常快)。

我见过这样的图片:

[![bezier graph example](img/bd4687a8e3979ae40dd127f8128560fa.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gzgSKCVW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x1kvb2quxwy1d85ofbaj.jpg)

但是我一直不明白如何阅读一个贝塞尔曲线函数或者如何手写一个(有人手写这些吗？)

为了让这个更有用一点，假设我是一个五岁的孩子，对代数和平面几何有相当的理解。