# 苹果 ARKit——主流增强现实

> 原文：<https://dev.to/jisc/apple-arkit--mainstream-augmented-reality-ddk>

有没有穿过一个入口进入另一个维度，或者想知道如果你能进入一个 CAD 模型或解剖模拟会是什么样子？这是苹果新的增强现实技术的承诺，是 iOS11 的一部分，IOs 11 是驱动数亿台 iPads 和 iPhones 的最新版本的操作系统软件。

[![Turning the IET into a Mario level using Apple ARKit](img/c9f7ed1ce93d29e6a3cf688effa62991.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--S2qubP3y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://innovativetechnology.jiscinvolve.org/wp/files/2017/09/IMG_0354-300x225.png)

使用苹果 ARKit 将 IET 变成马里奥关卡

增强现实已经存在多年了，但方式非常有限——将你的手机/平板电脑摄像头对准一张有特殊标记的照片，AR 应用程序通常会做一些事情，比如激活视频或向你展示 3D 模型。

但是，过去任何想要开发 AR 应用程序的人都不得不应对几个大问题——首先，手机和平板电脑的硬件还不能胜任实时图像处理和位置跟踪的工作，其次，还没有一种标准的方法来为应用程序添加 AR 功能。

随着处理器技术的最新进步以及我们的设备上搭载的更强大的图形和人工智能协处理器，该技术现在已经达到了实时位置跟踪可行的水平。有传言称，苹果将在即将发布的 iPhone 8 上安装一个类似于谷歌 [Project Tango](https://get.google.com/tango/) 设备的传感器，该传感器将支持实时深度传感和遮挡。这意味着您的设备将能够分辨虚拟世界中的对象与现实世界中的对象之间的位置关系，例如，是否有一个人站在虚拟对象前面？

苹果和谷歌也在通过将 AR 功能添加到他们的标准开发框架中来解决标准化问题——通过苹果设备上的 ARKit 和 Android 设备上即将到来的 [ARCore](https://developers.google.com/ar/) 。苹果在这方面处于领先地位，作为 iOS11 预览版的一部分，它已经允许开发者访问 ARKit。这意味着几乎有数百名开发者已经知道如何创建 ARKit 应用程序。我们可以预计，在 iOS11 正式发布后不久，将有许多令人兴奋的新 AR 应用出现在 App Store 中——很可能是 iPhone 8 发布公告的一部分。如果你是一名开发者，你可以在 GitHub 上找到大量的[演示/原型 ARKit 应用。【【编辑:这篇帖子写于 iPhone 8 / X 发布会之前！]]](https://github.com/olucurious/Awesome-ARKit)

在今年的[Times Higher Education World Academic Summit](http://www.theworldsummitseries.com/events/the-world-academic-summit-2017/)上，作为 Jisc Digi Lab 的一员，我制作了一个视频，展示了人们制作的几个演示应用程序，并让您对它的使用有一点了解:

如何才能看到人们在研究和教育中使用 ARKit？想象一下，拿起你的手机，发现你周围的 STEM 实验室的设备都贴有它们的名称、文档、“保留我”按钮之类的标签——可能还有一个图形状态，表明你是否接受了使用该套件的健康和安全指导。或者想象一个潜在的学生参观，在那里潜在的学生可以举起他们的手机，看看每栋楼里发生了什么，巨大的箭头出现，指引他们到下一个活动，诱导会议，学生会社交等。

很容易想象 AR 在苹果地图和谷歌地图等导航应用中得到广泛应用——并且该技术将从我们面前的屏幕飞跃到我们佩戴的屏幕(眼镜！).这里有一个来自松田隼敬一的视频，它想象了当增强现实眼镜成为常态时，未来可能会是什么样子:

你将如何在研究和教育中使用 ARKit？也许你已经有计划了？请在下方留言分享你的想法。