# 改善 MacBook Air 的显示图像

> 原文：<https://dev.to/obender/improving-the-display-image-of-macbook-air-alf>

自 2011 年以来，苹果一直在 MacBook Air 中使用三星和 LG 的屏幕面板。这两种显示器的质量都非常高，但是，LG 面板在苹果的标准颜色配置文件下似乎工作得更差。这让画面显得有些平淡和苍白。

声明:我个人推荐在[亚马逊- MBA](https://www.amazon.co.uk/gp/product/B072N2FRY9/ref=as_li_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=B072N2FRY9&linkCode=as2&tag=websell-21&linkId=767211c6b2df6b56f1dc378db9938b4a) 上买一个

要修复这种情况，只需将 MacBook Air 的标准颜色配置文件替换为修改后的颜色配置文件即可。实际上，事实证明，这不仅会改善 LG 矩阵的形象，还会改善三星的形象。无论如何，它发生在拥有 MBA 13”(2013)的我身上。
我们检查了矩阵制造商

要找到显示器的制造商，请启动终端(/ Programs / Utilities /)并运行以下命令:
$ ioreg-lw0 | grep iodisplayedid | sed "/[^&lt；] *

在输出端，您将得到类似 LTH116AT01A04 的内容。它代表一个简单的问题:

*   LP — LG
*   其他一切——三星

如果感兴趣，那么你可以把这个代码输入谷歌，重新检查制造商。但是，从评论来看，新的颜色配置文件不仅有助于 LG 的矩阵，也有助于三星的矩阵。
更改色彩配置文件

我试了几个 MBA 的颜色配置文件，最喜欢的是 MacRumors 版本。如果你感兴趣，那么在论坛的那个分支你可以找到其他选项。因此:
下载并解压缩带有配置文件的归档文件—自定义配置文件
打开 Finder 并按 Cmd Shift G

在出现的窗口中，输入
/Library/ColorSync/Profiles/Displays/

“显示器”文件夹会显示在 Finder 中。将 CustomMBA.icc 配置文件复制到其中。
现在转到系统设置→显示器→彩色，取消选中仅此显示器的配置文件。我们的新配置文件将被命名为 MacBook Air。

一旦你选择了它，你会立即注意到不同之处。要把所有东西都退回去，只要选择苹果的标准版就可以了。
就像我上面说的，我的 matrix LTH116AT01A04(三星)也开始表现的比较好。
图像变得稍微更暗、更清晰。
试试看，让我们讨论一下结果:)

我个人推荐在[亚马逊- MBA](https://www.amazon.co.uk/gp/product/B072N2FRY9/ref=as_li_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=B072N2FRY9&linkCode=as2&tag=websell-21&linkId=767211c6b2df6b56f1dc378db9938b4a) 上买一个