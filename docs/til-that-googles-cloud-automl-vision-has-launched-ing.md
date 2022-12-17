# 蒂尔:谷歌的云自动视觉是活的！

> 原文：<https://dev.to/nitya/til-that-googles-cloud-automl-vision-has-launched-ing>

## 我学到了什么

今天，来自谷歌([https://www . blog . Google/topics/Google-Cloud/Cloud-AutoML-making-AI-accessible-every-business/](https://www.blog.google/topics/google-cloud/cloud-automl-making-ai-accessible-every-business/))的费-李非博士【发布了 Cloud AutoML“愿景”产品】，这是他们更广泛的“民主化人工智能”使命的又一部分。

> ![Fei-Fei Li profile image](img/96d8e58653e02ee943c0652bb666c6ce.png)费-李非@ dr Feifei![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)自豪地宣布 AutoML Vision，一款 [#AI](https://twitter.com/hashtag/AI) 产品，使每个人都可以构建自己定制的 ML 模型，而无需太多的 ML 专业知识。为更多人实现 AI 民主化的一大步！令人惊叹的团队工作[@ Google cloud](https://twitter.com/googlecloud)[@ Google research](https://twitter.com/googleresearch)[@ Google brain](https://twitter.com/GoogleBrain)[goo.gl/MDzvrv](https://t.co/oZZeTqx2Gs)2018 年 1 月 17 日下午 15:42[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=953653745279102976)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=953653745279102976)249[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=953653745279102976)512

在高层次上，这允许开发者/企业做的是利用谷歌的云 ML 基础设施的规模&他们最先进的机器学习知识和模型——但带来他们自己的训练数据。通过这种方式，他们可以有效地向机器教授一组更相关/更有针对性的概念，从而提高效率，并在以后更有效地使用这些知识。

我还没有深入了解这个新产品，或者了解它的全部功能和局限性，但是我很乐意探索它。我已经可以想象出许多用途，我们收集的图像具有特定的(但非传统的)感兴趣的标签，现在可以突然被标记，从而在各种上下文中被发现。这就完全不同了。

## 这到底是为什么有趣？

出于多种原因，我对此很感兴趣。

1.  **AutoML as 产品。**我是在谷歌 IO17 上听到桑德尔·皮帅谈论 [AutoML 或“学会学习”](https://www.youtube.com/watch?v=92-DoDjCdsY)的许多人之一，这是他们推动机器学习变得更加主流的关键组成部分。从那以后，我看到了[云汽车](https://cloud.google.com/automl/)
    页面的推出，但没有看到任何具体的开发者友好的产品或服务围绕它推出。今天使 AutoML 的愿景更加明确(双关语)。

2.  **计算机视觉的定制训练模型。**我已经熟悉了现有的[谷歌云视觉 API](https://cloud.google.com/vision/) ，甚至对它进行了一点探索，对地标&标签的检测方面更感兴趣，这是一个与标记历史图像相关的热情项目。然而，这个 API 使用预先训练的模型，这对于泛型来说很好，但对于识别和标记特定实例来说并不总是完美的——特别是如果数据集不是公开可见/来源的，可以被认为是 Google 的“已知实体”。鉴于预先训练的模型使用了谷歌自己的(让我们承认，不是无关紧要的)图像语料库，仍有许多有用的信息需要获取——但当我将其应用于我的这个特别的激情项目时(即，“我如何才能获得一个扫描的历史照片库，并使用计算机视觉自动检测图像中出现的同一个人、地标、文本或标志？”)，我很快意识到定制培训是关键。

3.  **Google AutoML vs .[Clarifai Custom Training](https://www.clarifai.com/custom-training)**虽然我毫无疑问是谷歌技术的粉丝和倡导者，但我也是视觉识别人工智能领域的思想领袖 [Clarifai](https://www.clarifai.com/) 的超级粉丝，而 clari fai 恰好位于纽约市之外(耶！).我记得马特·泽勒在 2016 年 11 月向我们展示了他们的定制培训演示(在第一届[纽约发展节](https://devfestnyc.com))。在演示中，您可以获得一个自动注释的相册来识别“狗”的存在，但您也可以标记一个特定的实例(“Rufus”)并定制训练模型来识别/标记这些图片中特定狗的存在。当时，我还不知道有其他服务/API 能给我这种能力。从这个意义上说，我认为 Cloud AutoML Vision 现在是一个直接的竞争对手。这很有趣，因为正如人们所说，水涨船高。我相信研究人员和开发人员都将从这种竞争带来的新进展/理解中受益。

拿我来说，我很高兴能比较这两者——现在，如果他们能批准我的访问请求就好了:-)