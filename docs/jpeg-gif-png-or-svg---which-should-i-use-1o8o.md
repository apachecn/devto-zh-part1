# JPEG，GIF，PNG 或 SVG -你应该使用哪个？

> 原文：<https://dev.to/sarah_chima/jpeg-gif-png-or-svg---which-should-i-use-1o8o>

前端开发人员关心很多事情。图像管理和优化是其中的一部分。为什么会这样呢？据说图像占了 web 的 60%,所以作为一个前端开发人员，你很可能与图像打交道。

在我参加关于响应图像的课程之前，我并不关心我的图像使用哪种图像格式。我是说，随便给我一个..lol:)然而，上了那门课，实际上改变了我对图像的看法，改变了我对图像如何影响网页的看法，以及为什么总是使用正确的图像格式很重要。这是因为作为一个前端开发人员，你不应该只关心如何让你的图片在网页上显示得更好，你还希望它在不影响质量的情况下使用尽可能小的文件大小，你希望它响应更快，你希望它加载得更快。

有了不同的图像文件格式，我们可能会在每一点上面临一个至关重要的问题，我应该使用哪种图像格式？JPEG，PNG，SVG 还是 GIF？在我们得到这个问题的答案之前，让我们来看看这些文件格式。

### **JPEG**

JPEG 或 JPG 是联合图像专家组的首字母缩写。这是一种非常常见的图像格式。它是一种 24 位图像格式，这意味着 JPEG 图像可以合并和显示数百万种颜色，精确地说是 1680 万种颜色。这使得它们非常适用于超过 256 种颜色的照片和图像。

JPEG 以有损格式存储数据。因此，在压缩时，它们会丢失一些图像细节以使文件更小。这让我们可以自由选择 JPEG 文件的压缩程度。所以它通常非常适合制作较小的文件。但是要小心压缩，这样你就不会得到与原始图像差别很大的压缩图像。

JPEG 文件格式不适合包含大量线条和文本的图像，因为它们在压缩或放大后会因数据丢失而变得参差不齐。它也不支持透明性。

### **GIF**

图形交换格式(GIF)是一种已经存在了一段时间的图像格式。与 JPEG 不同，GIF 仅支持 256 种颜色。虽然这听起来像很多颜色，但实际上非常小，因为典型的照片通常有数千种色调。当照片被转换为 GIF 格式时，这些色调将会丢失，因此，GIF 对于具有大量颜色的照片或图像来说并不理想。

但是，GIF 可用于像素间差异较小的图形，如徽标、图标和旗帜。它非常适合简单的动画。与 JPEG 不同，GIF 支持无损压缩，即压缩时不会丢失任何数据。

GIF 的一个主要优势是它支持透明性的能力。这意味着你可以把 GIF 放在彩色背景或照片上，而不会看到它周围有任何边框。但是，GIF 透明度没有选择性。如果你使一种颜色透明，这种颜色将在整个图像中是透明的。多年来，GIF 是唯一支持透明的图像格式，但现在 PNG 和 SVG 也可以做到这一点。

### **PNG**

PNG(便携式网络图形)是一种为网络设计的图像格式。它吸取了 JPEG 和 GIF 的精华部分。它有两种格式:PNG-8 和 PNG-24。这两种格式的主要区别在于它支持的颜色数量。虽然 PNG-8 最多可以显示 256 种颜色，但 PNG-24 格式可以显示数百万种颜色，非常适合照片。

与 JPEG 不同，它是一种无损文件格式。因此，它保留了压缩后的质量和细节。这导致文件更大。因此，只有当图像质量比文件大小更重要时，它才应该用于图像。

PNG 图像比 GIF 更好地支持透明度。它还支持控制图像亮度、颜色校正和二维交错，这是一种渐进显示的方法。

PNG 图像还支持嵌入文本，它可以保存图像内容的简短文本描述。这有助于搜索引擎基于该嵌入文本的内容来搜索图像。

PNG 图像无法制作动画。并非所有的 web 浏览器都支持它。

### **【SVG】t1㎡型**

SVG(可缩放矢量图形)是一种矢量格式。它正变得越来越受欢迎，因为它可以做许多令人惊奇的事情。SVG 是用基于 XML 的标记编写的，它就像用于插图的 HTML 一样，与我们已经讨论过的其他图像格式有很大的不同。

SVG 是非常可伸缩的。它们是矢量，是从数学上声明的形状和曲线等绘制的，所以像素没有限制，不像其他格式依赖于像素。无论屏幕大小和屏幕放大倍数如何，它们看起来都是一样的。这对于响应式设计来说非常有用。

可以使用 CSS 和 JavaScript 修改 SVG。因此，如果您需要一个可以随时处理的图像，SVG 是一个很好的选择。它们也可以使用 CSS 动画和 JavaScript 来制作动画。

SVG 通常文件较小。SVG 文件的大小取决于它的复杂程度，而不是它的大小。因此，对于没有什么形状和线条的简单图像，SVG 可能比任何光栅图像(JPEG、PNG)都要小。但是对于复杂的图像或图形，它可能不是那么小，可能不是一个完美的选择。

SVG 最适合显示徽标、图标、地图、旗帜、图表和其他由线条和形状组成的图形。但是对于像照片这样包含大量线条和形状的复杂图像，这可能并不理想。虽然它在网上看起来很漂亮，但它不是一种我们可以用来在网上保存和上传图像的格式。

既然我们已经讨论了所有这些图像格式，让我们回到我们的大问题:我应该使用哪种图像格式？如果你仔细阅读以上所有内容，你会发现没有一种图像格式每次都能胜出。**没有最佳的图像格式。**你选择使用的图像格式取决于场景和你真正想要的。这是一张照片吗？你需要一个小文件吗？需要它在所有屏幕上都好看吗？需要动画吗？您希望能够修改它吗？你是否...这样的例子不胜枚举。

因为我想让你做出正确的选择，这里有一个每个图像格式的关键事实的回顾。相信可以指导你的选择。

**JPEG**

*   可以显示大约 1680 万种颜色
*   有损压缩
*   无法制作动画
*   非常适合静态图像和照片

**GIF**

*   可以显示 256 种颜色
*   无损压缩
*   支持透明度
*   非常适合简单的动画
*   适用于平面颜色的图形和没有渐变的图形

**PNG**

*   PNG-8:可显示 256 种颜色
*   PNG-24:可以显示数百万种颜色
*   无损压缩
*   比 GIF 更透明
*   无法制作动画
*   对图标有好处；当 SVG 格式不可用时可以使用

**【SVG】t1㎡型**

*   非常可扩展，非常适合响应式设计
*   可以使用 CSS 和 JavaScript 进行修改
*   较小的文件大小；取决于图像的复杂程度
*   可以制作动画
*   伟大的标志，图标，地图，旗帜，图表等。
*   不太适合拍照

### **号外**

嘿，还有一种图像格式变得很流行。这是 WebP 格式。你可以在这里阅读[。](https://developers.google.com/speed/webp/?csw=1)

需要更多关于我们主题的信息，这里有一些我觉得有用的文章。
[PNG，GIF，还是 JPEG？哪种图像格式最适合电子邮件？](https://litmus.com/blog/png-gif-or-jpeg-which-ones-should-you-use-in-email)

GIF、PNG、JPG 或 SVG。用哪个？

[为什么要使用 SVG？](http://svgtutorial.com/why-should-you-use-svg/)

[关于 SVG 你需要知道的一切](https://css-tricks.com/lodge/svg/)

有任何问题或补充吗？请留言评论。

感谢您的阅读。:)