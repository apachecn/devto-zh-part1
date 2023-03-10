# 揭示在 Android 中用图像创建类似 iOS 的分段控件的魔力(kot Lin)——第 1 部分

> 原文：<https://dev.to/staceypyee/revealing-the-magic-of-creating-ios-alike-segmented-control-with-image-in-android-kotlin--part-1--14hi>

**场景:**有三种 LED 状态供用户选择，即关、闪、开。Led 状态根据用户的选择进行更新。选定的状态将以蓝色背景和白色图标显示。

<figure>[![](img/c2b2d098627fdb720325299bc3794288.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VHPaPgAX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/640/1%2AP4_O-_pqw_hUpRQiLswghQ.gif) 

<figcaption>有三种 led 状态(关、闪烁、亮)供用户选择。默认情况下，Led 状态设置为“关闭”。</figcaption>

</figure>

在 iOS 中，我们可以通过使用[分段控制](https://developer.apple.com/ios/human-interface-guidelines/controls/segmented-controls/)来实现。安卓呢？

脑海中首先想到的是**按钮**视图。

### [按钮](https://developer.android.com/reference/android/widget/Button.html)还是 [ImageButton](https://developer.android.com/reference/android/widget/ImageButton.html) ？

根据 Android 开发者官方的输入控件 API 指南，ImageButton 是这个场景的最佳选择。

> 按钮表面的图像或者由 XML 元素中的 android:src 属性定义，或者由方法 setImageResource(int) 定义。

如 API 指南中所述，我们还可以通过为每个状态定义不同的图像来定制图像按钮的背景。

### 实现

*在本例中，@color/colorPrimary 设置为#0077c8，在本文中描述为“蓝色”

#### *第一步:XML 布局*

三个图像按钮放置在水平方向的 LinearLayout 中。

<figure>[![](img/edf09d63d9a2c44f6fa7766c6a9f9630.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lW2tXr8l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AmjemFXPV20gnKkqLHmX0jg.png) 

<figcaption>为 led 按钮状态布局。</figcaption>

</figure>

***表面图像***

按钮的表面图像由选择器文件定义，该文件包含两种不同状态的 led 图像:选中和未选中。

例如，在 drawable/selector _ led _ status _ Off 中(“关闭”按钮的表面图像)

<figure>[![](img/1182bc18886b9cb0765b27e2ed1e751e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dJLhNJe3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aov1RTJaXuqqfz89zCK1p3Q.png) 

<figcaption>选择关闭按钮时，显示 drawable/ic_led_off_selected(白色关闭图标)，否则显示 drawable/ic_led_off_normal(蓝色关闭图标)</figcaption>

</figure>

*P/S:闪烁和 on 按钮使用不同的图标资产集合实现与上面相同的选择器规则

***背景属性***

*< ImageButton >* XML 元素中的 *android:background* 属性用于根据用户选择的状态定义背景颜色。本例中选择的图像按钮是蓝色的。

***(一)纯色背景属性***

对于正常的实现，背景颜色的选择器可以通过简单地实现

<figure>[![](img/8c46a4be83a6fd4c3c5481a45a3665f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8R087MsJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A16DclZIhvtAa0a-Ma86Liw.png)

<figcaption>drawable/selector _ background _ colour . XML</figcaption>

</figure>

<figure>[![](img/1a42ced8f812eeb260a1dc506aac9148.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lob2xrVy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/640/1%2Auj5rGOCDn0JmErw0X-SuRg.gif)

<figcaption>Android 中使用 drawable/selector _ background _ colour . XML 的结果:背景属性</figcaption>

</figure>

***(二)背景属性带矩形彩色边框***

然而，在这个例子中，图像按钮是用蓝色边框包裹的。因此，这个实现需要一个具有 2 个形状项目的层列表**。第一层绘制边框，第二层是按钮的背景色。换句话说，画了两个矩形。一个矩形(背景色)绘制在另一个矩形(边框)的顶部。**

<figure>[![](img/9722f7ca82f2196499220a7a0f7d1d0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9xU6BgP5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AethXORoYar7F0MbCHZqpTw.png)

<figcaption>drawable/selector _ middle _ button _ state . XML:蓝色边框背景</figcaption>

</figure>

<figure>[![](img/f682bc7a57cdf53293a78afd50b5746d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cxoJPRUj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/640/1%2AeA4qnDa1lVxCXyAYTh9kgw.gif) 

<figcaption>安卓使用矩形彩色边框选择器的结果:背景属性</figcaption>

</figure>

***(iii)背景属性带蓝色圆角***

用<corners>定义<shape>的圆角半径。为此，我们可以使用 android:topLeftRadius、android:topRightRadius、android: bottomLeftRadius 和 android:bottomRightRadius 属性。</shape></corners>

下面是以蓝色边框为背景的左圆角按钮的实现示例

<figure>[![](img/a924d28296c047cbde9eff47708e511b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZhewQ4It--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AdGxa8bdsPiUJxOp6zs0kGQ.png)

<figcaption>drawable/selector _ rounded _ corner _ left _ button _ state . XML</figcaption>

T6】</figure>

*P/S:右圆角选择器文件对不同的角执行与上述相同的规则

#### *步骤 2:根据用户的选择(LedStatusFragment.kt)以编程方式初始化和更新 led 状态*

<figure>[![](img/66888cbe6b7908c4d97d00babfec0824.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C7Wh43id--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AAycstI4HILSTPyl3Exsjog.png) 

<figcaption>用相关字符串资源</figcaption>

</figure>

将 LedStatus 定义为 enum

[![](img/95e118d51022692e935d8a62df65b13d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZJRJLoOs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AWsqzBn0y61XJLStPxqB-VA.png)

第 49–59 行初始化每个 LED 状态的视图。默认情况下，关闭按钮被设置为选中状态

第 63–70 行更新了 led 图像按钮和指示用户选择的按钮状态的文本

最终结果如帖子的第一张图片所示

### 其他

用 RadioButton 能达到同样的效果吗？(待续……)

* * *