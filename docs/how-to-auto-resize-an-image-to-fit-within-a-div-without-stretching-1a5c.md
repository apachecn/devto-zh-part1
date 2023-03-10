# 如何在不拉伸的情况下自动调整图像大小以适应 DIV？

> 原文：<https://dev.to/moreonfew/how-to-auto-resize-an-image-to-fit-within-a-div-without-stretching-1a5c>

如果您正在开发一个响应式网页或流畅的网页，您肯定会遇到这样的情况:您希望在一个较小的 DIV 中放入一个大图像，而不扭曲它或破坏纵横比。嗯，这可以很容易地使用`max-width`属性来完成。

假设我有一个 768 像素宽的图像，我想把它放在一个 320 像素宽的 DIV 中。让我们把 DIV 称为“图形容器”。为了使图像适合“figure-container”DIV，我应该对图像使用`max-width`属性。因此，我的 CSS 可能看起来如下:

```
.figure-container img{ 
  max-width: 100%; 
  display: block; 
  height: auto 
} 
```

Enter fullscreen mode Exit fullscreen mode

这将确保图像的宽度不会超过 DIV 宽度的 100%。这意味着我的 768 像素的图像现在可以放在 320 像素的 DIV 中，同时还能保持纵横比。但是，请记住，根据您的场景，您可能也必须使用`display`属性。您可以将显示设置为`block`或`inline-block`。

另一种方法是对图像使用 object-fit 属性。

## 如何使用 CSS object-fit 在 div 中适配一张图片？

您还可以使用 CSS3 的`object-fit`属性使您的图像适合父 DIV。您所要做的就是给图像添加 object-fit 属性。对象匹配属性有 4 个值，它们是:

1.  **填充**–这将拉伸图像以适合内容框。
2.  **包含**–这将放大或缩小图像以填充内容框，同时保持纵横比。
3.  **cover**–这将在保持纵横比的同时用图像填充内容框，但可能会在此过程中裁剪图像。
4.  **无**–图像将保持其原始尺寸。
5.  **缩小**–使用 none 或 contain 属性调整图像大小，同时生成最小的具体对象大小。

```
.figure-container img{
  width: 100%;
  height:100%;
  object-fit:contain; /* This can be one of the 5 values from above */
} 
```

Enter fullscreen mode Exit fullscreen mode

当您使用`object-fit`属性来适应 DIV 中的图像时，您必须将它与`fill`、`cover`或`contain`值一起使用。然而，请注意对`object-fit`的跨设备和浏览器支持仍在发展中，所以请在使用它之前检查浏览器支持。有几个[polyfill](https://www.moreonfew.com/what-are-polyfills-in-javascript/)可用于对象拟合，但是请在实际使用之前尝试一下。

如何在不拉伸的情况下自动调整图像大小以适应一个 DIV？最早出现在 [MoreOnFew](https://www.moreonfew.com) 上。