# 提交了拉取请求，下一步是什么？

> 原文：<https://dev.to/cyberomin/a-pull-request-is-submitted-what-next>

几周前，我写了一篇文章，内容是当你考虑外包你的工程设计时，需要雇佣一个技术领导者。这篇文章引发了一些有趣的讨论，其中一个是关于拉式请求(PR)和代码审查(CR)以及应该如何进行。今天，我将尝试在团队成员中谈论代码审查及其固有的一些问题，并且我还将强调克服这些问题的可能方法。

对于上下文，我很乐意解释什么是拉式请求和代码评审，并且为了这篇文章，我将交替使用这两个术语。

> 拉式请求(PR)是一种向软件开发项目提交贡献的方法。这通常是使用分布式版本控制系统(DVCS，如 Git)向项目提交贡献的首选方式。当开发人员要求将提交给外部存储库的变更考虑包含在项目的主存储库中时，就会出现拉请求。Source - [OSS Watch](http://oss-watch.ac.uk/resources/pullrequest) >代码审查是对计算机源代码的系统性检查(有时也称为同行审查)。它旨在发现在初始开发阶段被忽略的错误，提高软件的整体质量。来源- [维基百科](https://en.wikipedia.org/wiki/Code_review)

我在大多数拉式请求/代码评审会议中看到的问题是，当评审人员在看似无关紧要的问题上吹毛求疵时，通常会忽略房间里的大象。这不仅降低了整个审查的体验，还导致了不必要的自行车脱落。在我看来，这是技术团队内部冲突的原因之一，是不健康的。

> 自行车棚的故事讲述了一个管理委员会决定批准一个核电厂，它这样做几乎没有争论或审议。这个故事与另一个选择自行车棚颜色的决定形成了对比，在这个决定中，管理层陷入了吹毛求疵的争论，并花费了比核电站决定更多的时间和精力。

拉式请求应该是一次学习的经历，它是一个从值得信任的团队成员那里获得良好反馈的机会。这种反馈的范围可以是从编写更加结构化和简洁的代码到更好地优化算法、查询等。是时候敞开心胸，不要变得不必要的防御了。

我发现一个非常令人担忧的问题是，大多数作者在提交他们的论文时都是束腰束腰的。你不能告诉他们，毕竟，他们已经花了一天的大部分时间来解决这个复杂的问题，他们需要的只是你的批准和合并到基础分支。这是一个大问题。

但是，作为一个公关作者，在你穿上盔甲之前，花一秒钟仔细想想你的评论者的意见，从他们自己的角度来看待这个问题。你的评论者是来揭露你的盲点的，他们检查那些肯定从缝隙中溜走的东西，他们是为了指导和帮助你变得更好。当作者像对待编辑一样对待他们的 PR/CR 会议时，他们会意识到他们一定忽略了什么，发现新的范例，并从根本上获得新的知识。你的审稿人就像编辑一样，是你的第三只眼睛。

你的评论者总是对的吗？远非如此。像每个人一样，你的评论者也不是绝对正确的。他们会犯错误，作为作者，你有责任引导他们明白显而易见的事情，帮助他们理解你对问题的思考过程，以及是什么导致了你的解决方案。我在公关会议上看到的一个问题是，评审人员在没有首先理解问题领域的情况下就急于证明一个观点。他们做出草率的结论，而且在大多数情况下，远远地错过了要点。

作为一个评审者，你的首要任务是理解问题领域并提供建议，而不是叫人们出来。这是大多数作者变得具有防御性的原因之一。别这样，你不想成为那样的人。在回顾的时候，如果有帮助的话，在可能的情况下，走到作者的桌前，或者打电话分享屏幕，然后你可以问问题，为什么作者做出了一些设计选择。这不仅能减少任何不可预见的紧张，还能让你更好地理解问题和解决方案，从作者的角度看问题，从而给你一个给出建设性反馈的机会。这种方法的另一个好处是它创造了一种纽带，使人们更容易接受你的想法。

当作者在他们的简历上留下的评论让他们看起来很愚蠢时，他们会变得有所防备。永远不要忘记，每个人都有自我。代码审查会议对每个人都是双赢的。审稿人和作者都从经历中学习和受益。在精心制作反馈时投入额外的思考和关注，它们应该具有启发性，并提供解决问题的更好方法。

请记住，当代码评审会议退化成一场激烈的竞赛时，它的本质就失去了，每个人，包括评审者和作者都失去了很多时间。

*本帖原载于[cyber omin . github . io](http://cyberomin.github.io/engineering/2017/04/27/pull-request.html)T3】*