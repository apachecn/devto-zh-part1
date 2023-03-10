# Kaggle Learn 点评:有深度学习的赛道，值得你花时间

> 原文：<https://dev.to/analyticalmonk/kaggle-learn-review-there-is-a-deep-learning-track-and-it-is-worth-your-time-2m6e>

从我开始学习机器的大学时代到今天，我对 [Kaggle](http://kaggle.com/) 的钦佩一直在增长。除了成为数据科学竞赛的代名词和普及数据科学竞赛之外，该平台还成为全球无数数据科学和机器学习从业者的发射台和滋生地，包括您的 true。事实上，我从这个平台学到的技能是我最近加入 [SocialCops](http://socialcops.com/) 的部分原因，这是一家我仰慕多年的公司。然而，2017 年我并没有像我希望的那样在平台上。所以当我看到[本·哈姆纳](https://twitter.com/benhamner)的推文推出 [Kaggle Learn](https://www.kaggle.com/learn/overview) ，一套交互式数据科学教程时，我下定决心要试一试。

> ![Ben Hamner profile image](img/25bf70b0bcfb970be4867d31cd249520.png)本·哈姆纳@本·哈姆纳![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)兴奋地推出 Kaggle Learn -关于机器学习、深度学习、R 和数据可视化的互动教程[kaggle.com/learn](https://t.co/vWITheLP7K)22:26PM-16 Jan 2018![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=953393146758537217)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=953393146758537217)

### 瞄准深度学习

Learn 目前有 4 个主题的教程——机器学习入门、R 编程、数据可视化和深度学习。三年多前，我第一次偶然发现了神经网络形式的机器学习。从那以后，我在不同的时间点研究了神经网络的理论细节，但有点讽刺的是，除了一些教程，我从未进入实际的深度学习。因此，我决定从深度学习开始。我之所以提到我过去在 ML 和 NN 的经历，是为了指出这样一个事实，当我开始学习这个课程时，我并不是一个完全的初学者，如果你是，那就从机器学习课程开始吧。

### 入门

如果你对神经网络不熟悉，或者最近没有接触过，在开始动手操作教程之前，先了解一些理论基础将是一个不错的主意。有许多介绍性的资源，包括文本和视频。我用了一个 YouTube 频道的 [3Blue1Brown](https://www.youtube.com/channel/UCYO_jab_esuFRV4b17AJtAw/about) 的精彩视频作为复习。

[https://www.youtube.com/embed/aircAruvnKk](https://www.youtube.com/embed/aircAruvnKk)

### 框架的选择

赛道使用了高级的 Keras API 和 T2 tensor flow 后端。即使有许多框架，这种组合似乎在深度学习社区的很大一部分中作为初学者友好的选择而受到青睐。就我个人而言，我钦佩 Keras 设计良好、用户友好，并在深度学习方法的民主化方面发挥了重要作用。

### 赛道

深度学习课程目前由六个部分组成。它们是:

*   *深度学习和计算机视觉简介*:从一个计算机视觉例子开始是熟悉机器学习的一个很好的方式。这是一个将深度学习置于聚光灯下的应用，数据(图像)是我们大多数人每天都要处理的东西。伴随的练习允许你玩基本的[卷积](https://en.wikipedia.org/wiki/Convolution)和图像。

*   *从卷积中构建模型* : [卷积神经网络](https://en.wikipedia.org/wiki/Convolutional_neural_network) (ConvNet)因在图像识别任务中取得了极大的成功而获得了广泛的赞誉和报道。讨论了 ConvNets 的基础知识，并为其实现搭建了平台。

*   *tensor flow 和 Keras 中的编程*:你第一次看到 TF+Keras 的运行，你会惊讶于你可以轻松地启动和运行。这里有很多工作要做，所以让代码单独工作不是很有用。尽可能地理解代码，包括帮助函数。

    **相关- [大深度学习新闻:Tensorflow 选择 Keras](//www.fast.ai/2017/01/03/keras/)T3】**

*   *转移学习*:丹·贝克尔[做出了一个伟大的决定，把这个包括进来，这也是我在教程中最喜欢的部分。在此之前，我对](https://www.kaggle.com/dansbecker)[迁移学习](https://en.wikipedia.org/wiki/Transfer_learning)的看法是，这是一个需要大量专业知识才能开始的高级话题。我很高兴地告诉你，我大错特错了。即使你所知道的只是神经网络的基础知识，迁移学习的想法本身就很吸引人，我已经决定在不久的将来花一些时间来研究这个话题。在开始这部分之前，我看了独一无二的吴恩达的视频。

[https://www.youtube.com/embed/yofjFQddwHE](https://www.youtube.com/embed/yofjFQddwHE)

*   *数据扩充*:简单来说，数据扩充是一种便捷的技术，可以为你的机器学习算法增加数据点的数量。本节讨论这种技术及其在 Keras 中的实现。

    **相关- [你需要知道的关于机器学习的数据增强](https://cartesianfaith.com/2016/10/06/what-you-need-to-know-about-data-augmentation-for-machine-learning/)**

*   *对深度学习的更深入理解*:前面几节中使用的代码，尤其是各种参数，都有更详细的讨论。此外，还简要讨论了随机梯度下降和反向传播。

### 结论和附加资源

当学习一个新的主题时，我总是发现最好从高层次的概述开始。这正是本专题讲座旨在提供的内容，并且在很大程度上提供了这些内容。在相当长的时间里，建立深度学习框架曾经是开始这个主题的一个障碍。为此，Kaggle 利用其平台的能力来托管代码，同时展示其在协作方面的潜力。尽管如此，这个主题只是触及了表面，即使比大多数教程都要好。你可以从这里开始规划你的道路。如果有帮助的话，下面是我计划在接下来的几周深入研究或探索的一些资源。

*   迈克尔·尼尔森的《神经网络和深度学习》
*   吴恩达和 deeplearning.ai 团队(MOOC)的[神经网络和深度学习](https://www.coursera.org/learn/neural-networks-deep-learning)
*   [杰瑞米·霍华德和 fast.ai 团队(MOOC)为程序员提供的实用深度学习](http://course.fast.ai/)

如果你能想到任何其他有用的资源，请在下面的评论中提出来。

如果你阅读并喜欢这篇文章，分享它将是一个很好的下一步。另外，你可以在 [Github](https://github.com/analyticalmonk) 上查看我的一些开源项目。
给我发一封[邮件](//mailto:akashtndn.acm@gmail.com)，或者在 [Twitter](//akashtndn.acm@gmail.com) 或 [LinkedIn](https://www.linkedin.com/in/akashtandon/) 上联系我，以防你想联系我。

**这篇文章最初发表在[科技与凡人](https://techandmortals.wordpress.com/2018/01/27/kaggle-learn-review-there-is-a-deep-learning-track-and-it-is-worth-your-time/)上。**