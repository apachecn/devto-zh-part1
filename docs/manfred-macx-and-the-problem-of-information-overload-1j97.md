# 曼弗雷德·麦克思与信息霸主问题

> 原文：<https://dev.to/allanmacgregor/manfred-macx-and-the-problem-of-information-overload-1j97>

我一直对查尔斯·斯特罗斯(Charles Stross)的书 *Accelerando* 中提出的技术概念、人造猫、意识下载、纳米技术等着迷。但其中有一个引起了我的特别注意；曼弗雷德 Marcx 眼镜。

眼镜？眼镜有什么特别的？嗯，这不是一副普通的眼镜，事实上，这是一副增强现实眼镜，可以告诉曼弗雷德最近的新闻、搜索代理的结果、电话、导航指令以及他想要的任何信息。

我们可以称之为**智能眼镜**，在书中，这款眼镜让曼弗雷德置身于技术和信息的大出血时代；没有他们，他就无法发挥作用。

> 在查尔斯·斯特罗斯(Charles Stross)的《Accelerando》中，小说的第一部分以曼弗雷德·迈克斯(Manfred Macx)的故事为开头，他是一位超级连通的后经济企业家，依赖于声誉和善意，而不是传统的货币财富。——**来源:[后人类，后记忆:查尔斯·斯特罗斯《T2》](https://www.academia.edu/1247436/Post-human_post-memory_Mnemotechnology_in_Charles_Stross_Accelerando)**中的记忆技术

这里吸引人的部分不是增强现实眼镜本身，而是为它们提供动力的系统，即外皮层。

> 外皮层可以被最好地描述为跨人类或后人类实体的大脑(或皮层)的一部分，它存在于该实体的主要计算结构之外，通常是居住在人的“肉体”中的大脑。例如，一个人的外皮层很可能由所有外部记忆模块、处理器和人的生物大脑实时交互的设备组成，从而实际上使这些外部设备成为个人“思维”的功能部分。— **来源:[Wikibooks](https://en.wikibooks.org/wiki/Accelerando_Technical_Companion)T3】**

虽然我们距离能够在外部硬件的帮助下直接增强我们的生物大脑还有几年甚至几十年的时间，但当前的技术应该足以至少创建一个 exocortex 系统所需的软件的早期版本。

## 定义实现

对于这样的项目，第一直觉是去开发一个复杂的架构，也许是一个多代理系统；是的，那会很酷……而且非常复杂。但是，如果稍微转换一下概念，如果我们使用微服务架构，而不是一群智能代理，会怎么样呢？

那可行吗？由于以下原因，该架构会成功还是失败？

*   网络延迟
*   消息格式
*   容错
*   部署复杂性

_ **注:**该系统不仅要帮助数据消费和处理，还要帮助信息生成。_

> 如果我们不利用我们接受的信息做点什么，那么我们只是媒体低谷中的猪。

这就是挑战所在，我可以建立一个给我信息的系统，当然。但是我能建立一个系统，帮助我更快更有效地生成内容吗？

> 元大脑就是网络——我们不断增长的信息、人员、传感器和计算设备的全球网络。

这是否被认为是外皮层或更类似于元皮层，所有相关的问题，但除了最初的原型实现点。

更有趣的是像这样的分布式系统的优点，以及它可以提供的可邮寄性。由微服务组成的系统不会关心每个服务的实现；只要他们尊重共同的沟通契约。

> 网络实际上是人脑的一个新层次，超越了任何个人的大脑。它是一个全球大脑，将我们所有的大脑连接在一起。它是智能的。这也许是人类最伟大的发明。它以我们个人难以理解或预测的方式集体感知、反应、解释、学习、思考和行动，这种活动构成了一种新兴的全球思维。— **来源:[新星斯皮瓦克](https://www.novaspivack.com/web-3-0/a-new-layer-of-the-brain-is-evolving-the-metacortex)T3】**

尽管即使在分布式服务架构中，也必须有一个协调中心，一个负责定义和运行任务的系统或流程。

# 选项 1

*   创建一连串的事件和服务
*   链中的每个服务回调到内联的下一个服务
*   任务运行者只关心事件链的开始

# 选项 2

*   分离服务
*   中央系统为每一个实现一个适配器
*   运行任务的每个步骤，传递相关数据
*   可以跟踪每项任务的进度。

此时，选项 2 似乎更有吸引力，它还让我能够灵活地将第三方服务集成到这个结构中，而不必自己编写服务代码。我可以利用像 mashape.com 这样的网站来帮助我，至少是在最初的实施阶段。

> 外皮层是一个理论上的人工外部信息处理系统，可以增强大脑的生物高级认知过程。一个人的外皮层将由外部存储模块、处理器、IO 设备和软件系统组成，它们将与一个人的生物大脑进行交互，并增强其功能。通常，这种交互被描述为通过直接的脑机接口进行，使这些扩展在功能上成为个人大脑的一部分。— **来源:[维基百科](https://en.wikipedia.org/wiki/Exocortex)T3】**

## 缩小范围

为了衡量进步和成功，我需要首先定义可实现的目标，为了做到这一点，我必须缩小范围，并定义我想从我的“外皮层”中获得什么。

***我要…***

*   更好的**记忆**和指数化
*   帮助处理**数据**消耗
*   提高我的内容**输出**
*   **自动化**繁琐的任务

嗯，以上都是肯定的；但是现在哪一个对我帮助最大呢？基于我当前的目标，以及我当前面临的挑战；数据消费和随后的内容输出将非常方便。

尽管如此，范围似乎很广；如果我们设置一个示例用例，我们可以进一步缩小范围。

### 例 1

```
EVERY [30min]
    GET [rss_feeds] OF [www.joelonsoftware.comundefinedblog.cleancoder.com]
        AND GET [subreddit] OF [programmingundefinedcodingundefinedphp]
        AND GET [hnews]
THEN
    DO [categorization] of {title}
WHEN
  {categories} HAVE_ONE_OF [tddundefinedbddundefinedagile]
  POST_TO [Summarizer Queue] 
```

Enter fullscreen mode Exit fullscreen mode

### 例 2

```
EVERY [30 minutes]
    GET [RSS feeds] OF [www.joelonsoftware.comundefined blog.cleancoder.com]
        AND GET [Subreddit feeds] OF [programmingundefined codingundefined php]
        AND GET [HNews New]
    THEN
        DO [Categorization] OF {title}
        DO [Classification] OF {title}
WHEN
    {score} GREATER THAN > [0.7]
    POST TO [Direct Email]
WHEN
  {categories} HAVE ONE OF [tddundefinedbddundefinedagile]
  POST TO [Summarizer Queue] 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来是什么？

听起来不错，到目前为止，我一直在为这个项目的架构工作了一年左右，已经有了一个数据收集的工作原型。有趣的部分开始了，因为我可以收集合理数量的数据，我该怎么处理它们呢？

我将在下一篇文章中讨论数据收集实现的细节。