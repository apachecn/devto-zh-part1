# 这里没有美化的 hello worlds

> 原文：<https://dev.to/elvispt/no-glorified-hello-worlds-here-4jbi>

在我开发软件的这些年里，大部分都是以网络为中心，总是缺少一些东西:单元测试。这是因为有些公司的文化憎恶任何导致延误的事情，甚至是合理的。从成本角度来看，一些不容易被证明是优势的东西会被扔出窗外。

我的经验仅限于有完美世界要求的个人项目。所以，最后，我认为自己是单元测试的初学者。

从哪里开始？我可以分析——并且已经分析过——实施了单元测试的项目，但是没有什么可以代替在真实项目中的用户和 it 边缘案例的实践经验。美化的“你好世界”不算数。

回过头一点，我明白了单元测试的优势。我已经看到了实施测试的项目，以及它带来的好处，但是不可能说服管理层相信它的重要性——甚至是其他高级开发人员。可能我不擅长沟通利弊。

最后，单元测试带来的另一个问题。测试就像数字安全。无论谁对此负责，都将永远处于忘恩负义的辩护地位。当一切进展顺利时，单元测试正在正确地完成它的工作，开发人员收获了一些好处，有人可能会说——或者至少会想:

> 如果一切进展顺利，为什么我们还要在单元测试上浪费时间呢？我为什么要付钱给你？

或者甚至当**不可避免的**bug 发生时:

> 为什么我们会有问题？我以为我们有单元测试。我为什么要付钱给你？

[![](img/3e50131abe3b53cca34fb54157ffb6fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q8m6a44P--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ACfdxLFqER3D-buDMmBh5sw.jpeg) 

<figcaption>照片由[edu·劳顿](https://unsplash.com/photos/5oyFrBF33Q4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)于 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

这是令人恼火的。

有一次，我会见了一家新公司的团队，我最终加入了这家公司，我记得我问“你做单元测试吗？”。快乐的回答是“当然”。我很高兴我将加入一家公司，在那里我可以获得在真实项目中进行单元测试的经验。我不知道他指的是最终用户测试。有一个团队专门测试整个用户流程，从开始到结束，以确保一切都符合要求。这很好，但是这不是单元测试。我后来发现，这家公司有一个令人讨厌的习惯，那就是使用行业标准名称，在公司内部意味着完全不同的东西。顾问？=被美化的推销员。

当即使是经验丰富的开发人员也不想或不关心单元测试时，阐明这些优势似乎很难。在现实生活中，我们还必须考虑公司政策和个人利益——我希望这个项目尽快完成！这通常意味着离奖金更近了。

通常有哪些优点？我们来列举一下。

*   尽早发现 bug—越早发现 bug，它出现在生产中的可能性就越小。
*   提高代码质量和可读性——这是大型项目的一大优势。
*   促进变更，让开发人员更有信心他的变更不会破坏现有的功能，因此…
*   …提高开发人员的工作效率。

综上所述，你得到了最大的利润，因为从中长期来看，它降低了成本。

> 我可以分析实现了单元测试的项目，但是没有什么可以代替在真实项目中用户和 it 边缘案例的实践经验。美化的“你好世界”不算数。

缺点是什么？让我们来看看:

*   不保证代码没有错误。这是显而易见的，但是不知何故有些人相信单元测试是神奇的。
*   耗时。就等于说成本更高。不能按时发布项目，或者项目必须有功能削减。经理/客户不喜欢听到这些。
*   单元测试可能会有错误。这取决于开发团队的质量。这里纪律不严，你会有麻烦的。

归结起来就是成本。不允许开发人员失败的公司文化不会有单元测试。如果你不能指望队友的帮助，你就不会有单元测试。如果你的公司只把你看作 excel 电子表格中的一行，那就意味着你实际上会被单元测试(成本)烧伤，然后你就不会有单元测试了。

[![](img/503b10fc09f6b01cf515d1916fc13fee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nIzM3WfH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJsodd7y2uNxBeWED_ufuHg.jpeg) 

<figcaption>照片由[大肠杆菌](https://unsplash.com/photos/82TpEld0_e4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T8】</figcaption>

在阅读了很多关于单元测试的内容后，我发现最大的好处是它让我们专注于并反思编写的代码。如果你的代码太大或者难以阅读，你很可能会重构它。在一个团队中，这将通过更高质量的代码库提高生产率。作为一名开发人员，我不需要更多的理由来喜欢单元测试。