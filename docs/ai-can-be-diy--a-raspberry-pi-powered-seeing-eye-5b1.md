# 人工智能可以 DIY——一个树莓 Pi 驱动的“导盲眼”

> 原文：<https://dev.to/jisc/ai-can-be-diy--a-raspberry-pi-powered-seeing-eye-5b1>

现在几乎没有一天没有令人窒息的报纸标题，关于人工智能(AI)将如何把我们所有人变成超人，如果它最终没有首先取代我们的话。但是，我们所说的人工智能到底是什么意思，我们能用它做什么？在这篇文章中，我将看看技术的现状，以及如何使用一台便宜的 [Raspberry Pi](https://www.raspberrypi.org/) 计算机和谷歌的一些免费软件 [TensorFlow](http://www.tensorflow.org) 来构建自己的“导盲眼”人工智能。

如果你想尝试自己做这件事，我正在遵循 BBC R & D 的 Libby Miller 制作的精彩的[一步一步指南](https://planb.nicecupoftea.org/2016/11/11/a-speaking-camera-using-pi3-and-tensorflow/)。我还应该指出，这是可能的，因为 Sam Abrahams，他让 [TensorFlow 在树莓 Pi](https://github.com/samjabrahams/tensorflow-on-raspberry-pi) 上工作，并且[也写了关于 TensorFlow 的书](http://www.samabrahams.com/)。

[![](img/d25666654a2416eb44e0bb80189f8351.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WH3xGzIQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://innovativetechnology.jiscinvolve.org/wp/files/2017/09/IMG_20170519_153804-300x225.jpg)

Raspberry Pi 标志丝网印刷在主板上

人工智能目前主要专注于模式识别——静态和动态图像，但也包括声音(识别单词和句子)和文本(这种文本是用英语写的)。一个很好的例子就是上图中的 Raspberry Pi 标志。尽管有点模糊，我们看不到整个画面，但大多数人会认出照片中有某种浆果。熟悉这种小型低成本电脑的人几乎可以立即识别出图片中有 Raspberry Pi 标志——电路板背景可能有助于唤起他们的记忆。

当我们谈论“智能”时，事实是这种模式识别是相当愚蠢的。如果有的话，这种智能是由人类添加一些规则来提供的，这些规则告诉计算机要寻找什么样的模式，当它匹配一个模式时要做什么。所以让我们来做一个小实验——我们将在树莓派上安装一个摄像头和一个扬声器，教它识别摄像头看到的物体，并告诉我们它在看什么。这是一种非常缓慢笨重的低科技版本的 OrCam，这是一种帮助盲人和弱视者独立生活的新发明。

[![Our Raspberry Pi powered "seeing eye" AI](img/71f6c37d38694726408487a6736831b3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--de1_c8Vu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://innovativetechnology.jiscinvolve.org/wp/files/2017/09/Screen-Shot-2017-09-16-at-16.17.58-1024x478.png)

我们的 Raspberry Pi 驱动的“视觉”人工智能

树莓派耗电很少，所以你实际上可以用电池来运行它，尽管它不如 OrCam 那么便携或时尚！除了扬声器，你可以简单地插入一副耳机——但扬声器让我的演示视频效果更好。我用的是树莓派 3 型(25 英镑)和官方树莓派相机(29 英镑)。如果你想知道这些线是干什么的，这是我用树莓 Pi 的通用输入输出(GPIO)连接器为相机的快门按钮提供的廉价而愉快的替代品。GPIO 可以轻松地将各种硬件和扩展板连接到您的 Raspberry Pi。

这就是硬件，那么软件呢？那是我们人工智能的真正大脑…

谷歌的 TensorFlow 是一个开源的机器学习系统。机器学习是支撑大多数现代人工智能系统的技术，它负责我刚才谈到的模式识别。谷歌迈出了大胆的一步，不仅让 TensorFlow 免费可用，还通过“开源”让每个人都可以访问该软件的源代码。这意味着全世界的开发者都可以(也确实)增强它并分享他们的改变。

机器学习的问题是，你需要给你的人工智能提供大量的例子数据，然后它才能成功地进行我刚才谈到的模式识别。想象一下，你正在与一辆自动驾驶汽车合作——在它能够合理地确定一只跑到汽车前面的猫是什么样子之前，人工智能需要训练。你通常会通过向它展示大量猫跑到汽车前面的照片来做到这一点，这些照片可能是在你的人类驾驶员辅助测试运行期间收集的。你还会给它看很多它可能遇到的不是猫的其他东西的照片，并告诉它哪些照片里有猫。在引擎盖下，TensorFlow 构建了一个“神经网络”，这是对我们大脑工作方式的粗略模拟。

因此，让我们给我们的 Raspberry Pi 和 TensorFlow 驱动的人工智能一个旋转——观看下面的视频:

现在坦白说——我自己并没有坐下来教它几个小时。相反，我使用了现成的 TensorFlow 模型，谷歌已经使用超过 1400 万张图片的免费数据库 [ImageNet](http://www.image-net.org/) 对其进行了训练。在 Raspberry Pi 本身上构建这个模型需要很长时间，因为它不是很强大。如果你想自己创建一个复杂的模型，并且没有超级计算机，你可以从谷歌、微软和亚马逊等公司租用计算机来完成这项工作。

所以现在你看到了我的“导盲眼”AI，你会用 TensorFlow 做什么？为什么不留下评论让我知道呢…