# 从开发人员犯的 10 大错误中学习

> 原文：<https://dev.to/charanrajgolla/learning-from-top-10-mistakes-done-by-developers>

我给你列出了新开发人员(有时甚至是有经验的)通常会犯的一些常见错误。我相信从这些错误中吸取教训会对你有所帮助。所以，这里是我挑选的**十大错误**。

## 10。信任用户的输入

永远不要相信最终用户提供的输入。也不怪他们，多半是他们无心为之。我们都会犯错误。这可能不是他们非常关心的问题，但一个坏数据可能会导致应用程序停机或引起与合规性相关的问题。除了天真的非技术用户之外，我们也有具备一定技术知识水平的用户，他们可能会利用这些知识来绕过系统并完成他们的工作。这些用户对您的应用程序构成威胁，您的应用程序需要防范恶意活动。

避免以后陷入麻烦的唯一选择是用自我解释和人类可理解的错误消息构建一个强大的验证。晦涩的信息很难帮助天真的用户完成任何任务。应该在**用户界面和数据库级别**进行验证，以确保即使通过某种方式关闭了前端验证，应用程序仍然不接受垃圾值。

## 9。手动单元测试

您所做的代码更改，需要经历单元测试和进一步的质量保证测试。需要进行所有的测试或至少一些最小数量的测试，以确保您在一个代码单元中所做的更改不会对其他代码单元产生副作用。这是必要的邪恶。让**自动化单元测试和集成测试**在验证变更的副作用方面为您节省了大量的人工工作。

## 8。跳过文档

你能做的最糟糕的事情就是跳过文档。当然，你可以阅读源代码并尝试理解其功能。从代码中逆向工程功能是一项艰巨的任务。对于一个微小的功能来说，这已经足够好了，但对于整个应用程序来说，这绝对不够。

> 需求变更、代码变更和更频繁的开发团队成员变更

关于应用程序的知识可能不会 100%地转移。有时候人们就是会忘记。你不需要创建几十个文档，只创建几个类似的需求规格和技术文档，并确保保持它们**更新**，这应该是一个严格过程的一部分，需要不惜任何代价来遵循，并且必须适应项目规划。这在应用程序处于支持/维护阶段的后期阶段会有所帮助。

## 7。忘记审计和错误记录

并不是所有的最终用户/利益相关者都会为审计日志和错误日志这样的横切关注点而烦恼。一个利益相关者会简单地提出一个线性需求，你必须深入下去，直到它变得足够好，并交付一个解决利益相关者问题的解决方案。

数据变更审计和错误日志记录可能永远不会成为用户需求的一部分，但这意味着需要维护它。有一天，用户只需联系支持团队，要求他们提供与访问和角色相关的数据，或者出于合规性或审计目的所需的一些关键业务数据，然后您就没有什么可分享的了。

错误记录也很重要，你可以做防御性的编码，但是你永远不知道在这种情况下什么会失败，有效的错误记录会派上用场。监控错误日志并尝试解决重复出现的错误将有助于使您的应用程序更加稳定。

## 6。不小心使用特权访问

首先，绝对不应该提供完全控制和访问。如果您泄露了超级用户或特权帐户密码，系统将无法保护自己免受恶意活动的攻击。轻率使用特权访问所涉及的风险应该成为开发或支持团队中缺乏经验的新成员学习的一部分。有时，您可能必须对生产数据库进行修改，拥有特权访问可能会导致许多问题。

> “权力越大，责任越大”

我记得有一次无意中从主表中删除了数据，查询被自动提交。我全身发冷，但很快意识到我已经备份了数据，唷！通过保留数据备份的接口提供有限的数据修改权限总是好的。人们还应该注意到，这个工具还应该能够进行批量操作，否则你会发现自己要做很长时间的手动更新。

## 5。构型威胁

分散在**不同文件和数据库中的配置是一个威胁**。影响多个应用程序的配置更改必须在许多文件和数据库中复制。如果您有少量应用程序，这可能不是什么大问题，但是当您在企业环境中有数百个应用程序时，这很快就会成为一个难以应付的任务。此外，配置文件中的裸密码是另一个威胁。如果配置是数据库驱动的，那么它可以减少后期风险，还可以保护敏感的配置条目。

## 4。硬编码定时炸弹

在程序中硬编码值绝对是一种不好的做法。奇怪的问题可能会发生，因为硬编码的值静静地存在于您的源代码中。一个这样的例子是当你使用硬编码值作为最大值时，假设不会达到最大值。这种情况可能需要几个月或几年的时间来提出一个错误。再加上没有错误日志记录，您将处于非常糟糕的境地。尽量避免硬编码的值。如果需要，将它们移动到配置变量中，或者尝试完全避免需要它们的情况。

## 3。防止烧毁

不间断地工作只会导致精疲力竭。你应该每隔一段时间就休息一下。当你过度劳累时，即使做简单的事情也要花很多时间。

为自己设定小的可实现的里程碑，不要忘记为此奖励自己。巧克力、蛋糕、快速游戏，任何能让你开心和自由的东西。学习如何防止精疲力尽会让你更有效率。

## 2。构建简历的代码/建议

拥有一份列出所有新技术的强有力的简历是很好的，但是不要仅仅为了夸大简历而这样做。学习它们，但是只有当你看到它们比现有的东西更好的好处和更高的可维护性时才使用它们。有时甚至不需要添加新的技术选项。

增加复杂性至少应该在提供易维护性方面有所回报。所以我坚信，当你提出一些解决方案时，要检查它是否使应用程序易于维护，并使每个人的生活变得容易。

## 1。在一项技术中找到舒适区

在一项技术中成为忍者是令人敬畏的，并且总是有益的。但是不要局限于一种技术。尝试**扩展你的知识**，当然有一个学习曲线，但挑战自我有助于你成为一个更好的技术专家——一个使用各种技术作为有效工具并提供业务支持解决方案的人。

时代在变，技术可能会过时，但你的学习永远不会停止。在使用新技术作为解决问题的解决方案之前，始终尝试探索新技术并尝试权衡利弊。

我想听听你对这些问题的看法。如果你有任何经验可以分享，请发表评论。记住…

> 唯一真正的错误是我们从中学不到任何东西的错误。~约翰·鲍威尔

*本文最初发表在 [SkillHive 博客](https://medium.com/skillhive)T3 上*