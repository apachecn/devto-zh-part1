# 像素和点

> 原文：<https://dev.to/onmyway133/pixel-and-point>

TL；DR:不要用`nativeScale`和`nativeBounds`，除非你在做一些非常低级的事情

### 什么是点和像素

来自[https://developer . apple . com/library/content/documentation/2d drawing/Conceptual/drawing printing IOs/GraphicsDrawingOverview/GraphicsDrawingOverview . html](https://developer.apple.com/library/content/documentation/2DDrawing/Conceptual/DrawingPrintingiOS/GraphicsDrawingOverview/GraphicsDrawingOverview.html)

> 在 iOS 中，您在绘图代码中指定的坐标和底层设备的像素是有区别的
> 
> 使用点(和逻辑坐标系)的目的是提供与设备无关的一致的输出大小。在大多数情况下，点的实际大小无关紧要。点的目标是提供一个相对一致的比例，您可以在代码中使用它来指定视图和呈现内容的大小和位置
> 
> 在标准分辨率的屏幕上，比例因子通常为 1.0。在高分辨率屏幕上，比例因子通常为 2.0

### [T3`scale`和`nativeScale`怎么样](#how-about-raw-scale-endraw-and-raw-nativescale-endraw-)

从[https://developer.apple.com/documentation/uikit/uiscreen](https://developer.apple.com/documentation/uikit/uiscreen)

*   var bounds: CGRect:屏幕的边框，以**点**度量。
*   var nativeBounds: CGRect:物理屏幕的边框，以**像素**度量。
*   var scale: CGFloat:与**屏幕**相关的自然比例因子。
*   var native scale:CG float:**物理屏幕**的原生比例因子。

### 比例因子和显示方式

有关设备及其比例系数的完整列表，请参见此处[https://www . paintcodeapp . com/news/ultimate-guide-to-iphone-resolutions](https://www.paintcodeapp.com/news/ultimate-guide-to-iphone-resolutions)

iPhone 6 和 6+推出**显示模式**[https://www . CNET . com/how-to/explaining-display-zoom-on-iPhone-6-and-6-plus/](https://www.cnet.com/how-to/explaining-display-zoom-on-iphone-6-and-6-plus/)

您可以看到，目前 iPhone 6+、6s+、7+手机在缩放模式下的比例因子为 2.88，在标准模式下为 2.6

你还可以看到，在缩放模式下，iPhone 6 与 iPhone 5 具有相同的逻辑大小

### 模拟器 vs 设备

这是为了向您展示在**缩放模式**下模拟器和设备中`nativeScale`的差异，因此也是`nativeBounds`的差异。

iPhone 6+模拟器

```
(lldb) po UIScreen.main.scale
3.0

(lldb) po UIScreen.main.bounds
▿ (0.0, 0.0, 414.0, 736.0)
  ▿ origin : (0.0, 0.0)
    - x : 0.0
    - y : 0.0
  ▿ size : (414.0, 736.0)
    - width : 414.0
    - height : 736.0

(lldb) po UIScreen.main.nativeScale
3.0

(lldb) po UIScreen.main.nativeBounds
▿ (0.0, 0.0, 1242.0, 2208.0)
  ▿ origin : (0.0, 0.0)
    - x : 0.0
    - y : 0.0
  ▿ size : (1242.0, 2208.0)
    - width : 1242.0
    - height : 2208.0 
```

Enter fullscreen mode Exit fullscreen mode

iPhone 6+设备

```
(lldb) po UIScreen.main.scale
3.0

(lldb) po UIScreen.main.bounds
▿ (0.0, 0.0, 375.0, 667.0)
  ▿ origin : (0.0, 0.0)
    - x : 0.0
    - y : 0.0
  ▿ size : (375.0, 667.0)
    - width : 375.0
    - height : 667.0

(lldb) po UIScreen.main.nativeScale
2.88

(lldb) po UIScreen.main.nativeBounds
▿ (0.0, 0.0, 1080.0, 1920.0)
  ▿ origin : (0.0, 0.0)
    - x : 0.0
    - y : 0.0
  ▿ size : (1080.0, 1920.0)
    - width : 1080.0
    - height : 1920.0 
```

Enter fullscreen mode Exit fullscreen mode