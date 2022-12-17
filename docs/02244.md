# 30 秒内的医学机器学习

> 原文：<https://dev.to/swyx/medical-machine-learning-in-30-seconds-dh1>

今天在 [DevFestNYC](https://devfestnyc.com/) , [乔什·戈登](https://twitter.com/random_forests)解释了 Tensorflow 如何被用于[检测癌症](https://research.googleblog.com/2017/03/assisting-pathologists-in-detecting.html)和[糖尿病](https://research.googleblog.com/2016/11/deep-learning-for-detection-of-diabetic.html)。神奇的是，这和训练一个识别猫狗的神经网络没有太大区别！以下是这次演讲的简要总结:

1.  **数据** -获取大量数据。你需要数百万张你正在训练的任何东西的图片。
2.  更多数据 -不，真的，你需要更多。有时你的数据可能是糟糕的，例如，如果你的医生小组互相不同意，或者更糟的是，不同意他们自己。(人类，谁需要他们？)
3.  **设置** - Tensorflow 现在附带了 Keras ( *牛逼*)，Keras 内置了[应用](https://keras.io/applications/)，其中 *InceptionV3* 相当不错( *awesomer* )，尽管 Josh 也向 NasNet [(一种 AutoML)](https://research.googleblog.com/2017/11/automl-for-large-scale-image.html) 喊出了作为训练自己的神经网络(*银河大脑爆炸*)。
4.  这里是活跃研究的艺术形式和领域。乔希解释了一些关键的想法，从使用[滑动窗口](https://medium.com/@ageitgey/machine-learning-is-fun-part-3-deep-learning-and-convolutional-neural-networks-f40359318721)到应用旋转/对比度/其他过滤器来尽可能地从图像数据中提取信息，这样你就可以，例如，识别它被训练识别的同一事物，即使它被上下颠倒。这里最有趣的部分是他们如何观察真正的医生如何在显微镜中查看载玻片，放大和缩小以获得不同的上下文，并通过复制和粘贴他们的代码 4 次并在同一数据集上以 4 种不同的缩放级别运行他们的模型来复制这种行为，从而获得了惊人的结果！！
5.  **Train**——这需要 2 天(Google Cloud)到一周的时间(拥有 10 个 GPU 的本地机器)。如果你是一名研究人员，谷歌免费提供 1000 个 TPU 给你使用，只要你在这里申请。
6.  部署(Deploy)——这实际上是最难的事情，让你的模型对普通的非技术人员有用。

我们的目标是让机器学习变得如此例行公事，令人厌烦。希望我已经让无聊变得有趣了！