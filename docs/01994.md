# 专业的 Scrum 团队构建有效的软件

> 原文：<https://dev.to/nkdagility/professional-scrum-teams-build-software-that-works-l1l>

我总是对将未完成的工作发布到产品中的团队数量感到惊讶。我明白，一个人可能需要几次冲刺，或者如果你继承了一些不好的东西，可能需要很多次，来偿还债务，但是如果超过了这个数目，那么你就不是一个专业的 Scrum 团队。我拥有的大量软件都是错误的，缓慢的，或者仅仅是没有完成的，这让我认为很少有[专业的 Scrum 团队](https://nkdagility.com/scrum-tapas-importance-professionalism/)！

## TL；DR；

每个组织都有权让他们的开发团队对他们开发的软件的质量负责，而不是数量。每个[开发团队](https://nkdagility.com/training/courses/professional-scrum-developer-training/)都应该通过 DevOps 实践、自动化和对每个版本细节的严格关注来追求工程上的卓越。有效的软件可以建立用户的信任，提升你的品牌，有缺陷的软件会引起不信任，损害你的声誉。有缺陷的软件和技术债务是当前组织不得不死死抓住传统泰勒主义管理技术的原因。

> 最终，如果你的组织不允许你以任何你喜欢的方式构建软件，那是因为你过去一直试图逃避交付的狗屎。你需要重新建立信任。

给你的团队上一堂专业 Scrum 开发人员的课，以加快 mup 的速度。

## Scrum 团队构建了有效的高质量软件

工作软件是没有错误或缺陷的软件。[开发团队](https://nkdagility.com/training/courses/professional-scrum-developer-training/)主要负责质量和交付工作软件。虽然他们也对价值交付负责，但责任在于产品所有者。这意味着，如果在交付缺乏质量的价值和提供更高质量的更少价值之间有一个选择，一个[开发团队](https://nkdagility.com/training/courses/professional-scrum-developer-training/)应该总是选择质量。

因为“规则是给聪明人的指导，傻瓜的服从”，我要提醒那些喜欢抓住绝对不放的人。因为你构建的任何软件都是组织的资产，并且所有资产都归因于你公司的价值，那么该软件一定存在于资产负债表的某个地方。如果您作为开发团队决定降低质量来交付产品，您是否会立即与首席财务官沟通，以便他们能够准确反映公司资产负债表上的价值损失？因为如果你不这样做，那么不管你知不知道，你的组织都有可能因为不准确地反映你的软件的价值而犯下欺诈罪！最终，降低质量的决定应该得到你的执行管理团队的完全同意和理解。

> 削减质量的决定不是由[开发团队](https://nkdagility.com/training/courses/professional-scrum-developer-training/)、产品负责人或 IT 管理层能够做出的，而是留给执行管理层的。
> 
> ## 优质软件不是关于期望的！

工作软件是没有错误或缺陷的软件，但是它不一定满足产品所有者或利益相关者的期望。

要理解每个人的期望是不可能的，更别说满足了，因此期望[开发团队](https://nkdagility.com/training/courses/professional-scrum-developer-training/)实现它们是不现实的。在每个 Sprint 的结尾，我们都会有一个 Sprint 回顾，我们邀请利益相关者和 Scrum 团队，停下来，根据已经交付的产品反思产品积压。在那里，您可以探索期望和交付之间的差异，然后更新产品 Backlog 以反映这种差异。Scrum 团队应该不断地调查他们交付的东西和涉众期望之间的差异，这样他们就可以尽可能地缩小差距，所以虽然他们有责任满足期望，但他们不能被追究责任。

> 开发团队由专业人员组成，他们在每个 Sprint 结束时交付潜在的可发布的“完成”产品增量。在 Sprint 评审中需要一个“完成”增量。只有开发团队的成员创建增量。
> 
> ——Scrum 指南【http://www.scrumguides.org/scrum-guide.html#team-dev

Scrum 指南故意不告诉你如何构建工作软件。它只声明它的交付是[开发团队](https://nkdagility.com/training/courses/professional-scrum-developer-training/)的责任和职责。如果你没有工作软件，那么你还没有在做 Scrum，尽管你可能正在朝它努力。

因此，要定义工作软件，我们必须看看什么不是工作软件:

1.  **已知错误或异常**–如果你发现了一个 bug，那么就修复它。如果太大，那就用 PO 提出来，拿到待办事宜上。太多的时间花在管理而不是修复错误上。修好它们。
2.  **手动测试**——如果你有手动测试，那么你已经在开发不工作的软件，或者你努力交付的软件。任何手动测试都是不可持续的，所以要实现自动化。
3.  **手动管道**–在 2017 年，任何人都不应该在自己的本地计算机上构建生产代码，更不用说从那里将代码交付生产。即使您的构建只是打包一些文件，并将它们推送到 FTP 位置。自动化你的构建过程……如果你有一个人不得不在代码和产品之间做一些不仅仅是批准的事情，那么你应该寻求自动化这个过程。人类会犯错，也会错过一些东西。至少有了[自动化流程，如果不是连续交付](https://nkdagility.com/continuous-deliver-sprint/)，你就能获得一致性，而且你可以随着时间的推移增加复杂性以获得一致性。确保你[自动化你的构建和发布管道](https://nkdagility.com/building-release-pipeline-release-management-visual-studio-2013/)。
4.  **没有源代码控制**–是的，我仍然喜欢没有源代码控制或者对它没有控制的组织。正是出于这个原因，我写了《现代源代码管理和开发入门[。如果你甚至没有源代码控制，不管你在开发什么，你都需要尽快得到帮助。没有它所暴露的商业风险太大了。](https://dev.to/mrhinsh/getting-started-with-a-modern-source-control-system-and-devops-13og-temp-slug-4418693)
5.  **缺少特性标志**——这是被拒绝的待定项的一个[基本谬误，你的工程团队将不得不找出如何在每次冲刺(或每次提交)结束时发布，而不管正在处理的 PBI 的质量如何。将未完成的功能隐藏在功能标志后面，这样最终用户就看不到它们，但是您的代码仍然可以发布。](https://nkdagility.com/the-fallacy-of-the-rejected-backlog-item/)

使工作软件变得困难的东西的另一个名字；技术债。上面列出的所有事情都是技术债务的形式，但是最大的形式就是质量差的代码。没有经过测试或者甚至不能通过另一个软件工程师粗略检查的代码。

## 如果开发团队不对质量负责，会发生什么？

如果开发团队没有对质量负责，你为什么相信你有呢？质量是软件中隐藏的度量之一，它可能存在也可能不存在，除非你生气地使用产品，否则你不会知道。如果你给一个[开发团队](https://nkdagility.com/training/courses/professional-scrum-developer-training/)施加交付压力，他们会持续不断地降低质量，以满足你给他们的任何荒谬的最后期限。

## 使用 Scrum 根据经验进行检查和调整

每个组织都需要专注于交付对客户有用的高质量工作软件。第一部分归[开发团队](https://nkdagility.com/training/courses/professional-scrum-developer-training/)所有，第二部分归产品负责人所有。这个[专业的 Scrum 团队](https://nkdagility.com/training/scrum-training/)然后在许多迭代中一起工作，试验并持续改进，以在这种情况下交付最好的结果。因此，与其成为一个业余团队，不如成为一个专业团队，来交付可工作的软件，因为这是你的组织和你的客户应得的。如果你在交付方面有困难，那么随时讨论你的选择，尤其是在你的 Sprint 回顾会上，找出你可以做的可行的改进，这将帮助你偿还一些技术债务并向前迈进。一旦这样的步骤可以确保你的[开发团队](https://nkdagility.com/training/courses/professional-scrum-developer-training/)至少通过[专业 Scrum 开发者](https://nkdagility.com/training/courses/professional-scrum-developer-training/)课程理解这一点。

使用经验主义来检查和适应 Scrum。

职业 Scrum 团队构建有效软件的帖子最早出现在 T2 的马丁·辛舍尔伍德-裸体敏捷有限公司。