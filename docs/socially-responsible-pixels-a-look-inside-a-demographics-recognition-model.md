# 社会责任像素？深入了解人口统计识别模型

> 原文：<https://dev.to/clarifai/socially-responsible-pixels-a-look-inside-a-demographics-recognition-model>

在 Clarifai，我们的团队[每天都在推进人工智能](http://blog.clarifai.com/train-your-own-visual-recognition-model-and-search-any-image-with-custom-training-visual-search/?utm_campaign=devto-org-account&utm_medium=blog&utm_source=dev-to&utm_content=article-link)的技术边界。但是，当我们探索新的技术前沿时，我们也敏锐地意识到[我们也正在进入新的道德领域](http://blog.clarifai.com/clarifais-a-i-code-of-ethics-or-how-to-prevent-a-robot-apocalypse/?utm_campaign=devto-org-account&utm_medium=blog&utm_source=dev-to&utm_content=article-link)。在计算机视觉的情况下，模型被训练成仅基于像素来判断图像。**那么，我们如何构建对社会负责的视觉识别人工智能，并在没有除像素之外的任何背景的情况下防止歧视性、攻击性或有偏见的结果？**

> “在不远的将来，人工智能将驱动我们生活的方方面面，从无人驾驶汽车到个人安全，再到我们看到的广告类型。你不希望影响你日常生活的东西的底层技术是一个可能有偏见也可能没有偏见的黑匣子。你希望能够影响技术，并在技术出错时教授它。这就是 Clarifai 的用武之地。”

带着这个问题，我们在创建新的[人口统计识别模型](https://developer.clarifai.com/models/demographics-image-recognition-model/c0c0ac362b03416da06ab3fa36fb58e3?utm_campaign=devto-org-account&utm_medium=blog&utm_source=dev-to&utm_content=article-link)时非常小心，该模型可以自动识别人们面部的年龄、性别和多元文化外观。我想借此机会向大家介绍一下我们公司试图解决的一些**道德问题，以及如何让我们的人口统计模型变得更好！**

[![https://developer.clarifai.com/models/demographics-image-recognition-model/c0c0ac362b03416da06ab3fa36fb58e3](img/1a4bb87a5d04b9725b961a3657c41187.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xUG9YM7l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://blog.clarifai.com/wp-content/uploads/2017/04/demographics2.gif)

## 我们人口统计模型和概念的正确术语是什么？

我们希望人口统计模型正确处理的第一件事是我们用于模型识别事物的术语。我们不仅希望这些术语能够准确地描述我们模型中的功能，还希望它们具有包容性和社会意识。虽然理解“年龄”是不言而喻的，但是“性/性别”和“种族/民族/多元文化外表”的概念却更加微妙。

### 什么是性对性别？

> “性别是一个人的性别(性别生物学)、一个人作为男性、女性、两者兼而有之(性别认同)的内在自我感觉以及一个人与这种感觉相关的外在表现和行为(性别表达)之间的复杂相互关系，包括他们的性别角色。GenderSpectrum.org

在 Clarifai，我们希望每个人都能感觉到他们可以以真实的自我来工作。尊重人们选择的自我认同方式的愿望影响了我们的决定，我们决定将“人口统计模型中的性别外貌部分”分为“男性”或“女性”因为我们的技术正在评估人脸的视觉特征，“性别”似乎是比“性”更合适的术语(性更多的是关于生理)。然而，我们认识到，“性别术语”的男性，“女性”，非二元等。在很大程度上是自我的一个方面，而不是我们认为我们的人工智能可以恰当地标记的东西——因此，我们用“男性”和“女性”作为描述符。

### 什么是种族 vs 民族 vs 多元文化的表象？

> “相同(通常)种族的人共享遗传的身体特征。同一种族的人有着共同的文化、语言、宗教和种族特征。种族性更广泛，也更有用。种族分类常常是外来者强加的，从科学的角度来看，许多传统的分类现在被认为是有问题的。因此，种族比民族更模糊，在智力上更不健全。”

我们很自豪在 Clarifai 有一个[多元化的团队，我们希望确保我们的人口统计模型代表我们的价值观。当应用于我们的人口统计模型时,“种族”和“民族”比“性别”对我们来说更是一个灰色地带。“种族”一词通常由身体特征来定义，这是我们的人口统计模型所认可的准确描述，但充满了负面含义。“种族远远超出了身体特征，因此也不能很好地描述我们的模型所看到的。**最后，我们决定创造一个新的术语，我们觉得这个术语包含了我们模型的功能和我们公司的价值观——“多元文化外观”。"**我们将“多元文化外表”中我们可以识别的默认概念与美国人口普查标准进行了对比。](https://www.tech-inclusion.org/)

## 如何在现实世界中使用我们的人口统计模型？

在 Clarifai，我们的核心使命是理解图像和视频，以改善生活。当我们构建新的视觉识别模型时，我们总是抱着这样的想法:我们的用户有着相同的意图——改善生活。然而，当我们谈论年龄、性别和多文化外观识别技术时，我们意识到并非所有真实世界的用例都是正面的。

最终，这是关于意图的，我们对人类有很大的信心。我们正在建立一个开放平台，全世界的开发者都可以在这个平台上开发下一代智能应用。限制开发者创造的东西不是我们的事，我们选择相信大多数人会利用我们的人口统计数据做好事——比如让人们关注[女性在科技领域的代表性](http://blog.rapidapi.com/2017/03/08/only-1-woman-in-top-100-stack-overflow-accounts/)或者结束[的人口贩卖](https://www.fastcompany.com/3069295/redefining-human-trafficking-in-the-us-will-help-end-it)。

## 我们如何让社区让我们的人口统计模型变得更好？

继续前面关于我们对人类的信仰的观点，我们也认为向社区开放培训我们的人口统计模型是确保我们有最好、最准确、最公正的模型的唯一方法。

我们一直让开发者能够通过我们的 API 向我们的模型发送反馈，现在我们让每个人都可以更容易地做出贡献。 [**我们网站上的现场演示现在允许您教我们的系统如何改进，而无需一行代码**](https://developer.clarifai.com/models/demographics-image-recognition-model/c0c0ac362b03416da06ab3fa36fb58e3?utm_campaign=devto-org-account&utm_medium=blog&utm_source=dev-to&utm_content=article-link)——只需在我们的演示中测试一个图像，看看结果如何，如果它们不是您所期望的，您可以纠正它们！[公众的反馈和参与度一样好](http://www.theverge.com/2016/3/24/11297050/tay-microsoft-chatbot-racist)，我们相信你能为我们提供合乎道德的反馈。我们将仔细地将这些反馈整合到我们模型的未来版本中，以提高它们的整体质量。

[![https://developer.clarifai.com/models/demographics-image-recognition-model/c0c0ac362b03416da06ab3fa36fb58e3?utm_campaign=devto-org-account&utm_medium=blog&utm_source=dev-to&utm_content=article-link](img/f6cd50e325a61f80d3b7b779d639e074.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Uc1ERUzV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://blog.clarifai.com/wp-content/uploads/2017/04/demographics.gif)

我们真诚地相信，我们平台上用户的多样性和您提供的视角是建立这个星球上最好的人工智能平台的唯一途径。我们的人工智能并不完美，在这一过程中会有失误，但我们希望通过赋予你教会我们的人工智能如何看待世界的能力，我们可以将真正的知识根植于*人工智能*。我们欢迎您加入我们的使命，了解每一个图像和视频，改善生活- [从这里开始](https://developer.clarifai.com/models/demographics-image-recognition-model/c0c0ac362b03416da06ab3fa36fb58e3?utm_campaign=devto-org-account&utm_medium=blog&utm_source=dev-to&utm_content=article-link)！