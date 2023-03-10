# 我的结对编程经验

> 原文：<https://dev.to/raulavila/my-experience-with-pair-programming>

大约四年前，我在马德里(西班牙)工作，我为一家使用极限编程作为方法论的公司做了一次面试。当时，我对那是什么一无所知，所以他们很快解释道:“每天早上，我们都有一个站立会议，在会上我们谈论我们前一天所做的事情。然后，我们查看写满便利贴白板，这些便利贴代表任务和每一对，因为在这里我们进行结对编程，选择一项任务并完成它。

这听起来和我所知道的完全不同(我仍然在做瀑布)...)，而且，说实话，我当时有点不情愿，主要是没看到自己全职和人家一起工作，共用一台电脑。“我就不能听音乐了！”，我想。这一点，再加上他们使用的技术没有引起我的注意，而且我在我工作的公司很舒服，让我拒绝了他们的提议。

现在是 2016 年。自 2014 年以来，我一直住在伦敦，当我做出搬到另一个国家的艰难决定时，我非常清楚，我会尽量利用这段经历。因此，从第一天起，我开始参加聚会、研讨会等，因为在伦敦你可以找到很多这样的活动。在许多这样的研讨会中，我们在案例研究中结对编程，目的是让我们自己熟悉技术、实践等。去年我参加了[全球代码静修日](http://globalday.coderetreat.org/)，那真是太棒了，在那里你基本上一整天都在和不同的人配对。

我开始觉得结对编程没那么糟糕。我非常喜欢它，我意识到我也学到了很多东西，而且，鉴于我对 TDD、像 [Spring Framework](https://projects.spring.io/spring-framework/) 这样的技术、云平台和许多其他技术越来越感兴趣，我找到了一家公司，在那里我将能够使用所有这些以及更多。

经过一年的结对，我想我现在可以给出我对**全职**结对编程的看法了。是的，我们从 9 点工作到 6 点，与另一个人共用一台电脑，但我们不共用键盘和鼠标，因为每个人都有自己的电脑。通常的结对方法是，一个人扮演司机的角色，负责编码，而另一个人应该避免接触设备，只是回顾和谈论两人正在做的事情。为了避免乏味，两种角色经常互换。

## 利弊得失

有很多优点，在我看来，它们完全战胜了缺点:

*   因为两个人在处理同一个问题，并且讨论了实现的每个方面，所以这两个人不会犯与我们单独工作时一样多的“愚蠢”错误。这种错误让我们浪费了很多时间，我想你们都知道我在说什么
*   你会学到很多东西:这是因为你的搭档总是知道一些你不知道的东西，从简单的东西开始，比如命令行工具，以及更通用的方面，比如我们正在使用的语言的模式或特性。当你用一种你不太熟悉的语言工作，但另一个人精通时，这一点发挥到了最大的潜力。我想不出比结对更好的学习新编程语言的方法了。相信我，体验超赞！
*   你教**吨**:同样，其他人会知道你不知道的事情，反过来也是如此，你会发现自己向你的搭档解释如何做某些事情。除了教学有多令人满意之外，你会发现教学是复习知识和发现漏洞的好方法
*   知识是共享的:如果你坚持极端的编程实践，结对轮换是频繁的，因此“单点故障”问题被有效地完全消除，并且如果团队中的一个成员有一天不工作，没有人会开始动摇。事实上，通过结对轮换，很难确定一个开发人员在团队中是绝对必要的。这可以打破很多公司的传统思维方式，因为它稍微挑战了一下企业阶梯模式。你知道，你拥有一个利基市场，这样更容易提升。如果你有这样的想法，停下来想一想，当你去工作的时候，你是在寻求个人利益还是团队利益。团队利益对你的公司来说总是好得多，因为公司会付给你一大笔钱来获得最大的回报
*   很好玩:人是社会性的存在，我们喜欢和人说话，讨论事情，开玩笑。结对编程一直鼓励这种做法，所以孤立的程序员在一天的大部分时间里听音乐的想法完全消失了。我个人认为，从长远来看，这对我们的心理健康也非常有益:)
*   这有助于获得更有凝聚力的团队。在《Peopleware》这本书里，我们可以读到“凝聚在一起的团队”的概念，即达到巡航速度且不可阻挡的团队。达到这个理想不是我们一天就能完成的，我想不出比结对编程更好的方法来实现这个目标
*   生成的代码有更少的错误:当我们进行结对编程时，我们一直在进行代码审查，所以错误的百分比大大降低了

很有可能我遗漏了一些东西，但是我认为在阅读完所有这些要点之后，你应该对结对的所有好处有了一个很好的了解

## 弊

正如生活中的所有事情一样，也有一些缺点，尽管我相信大多数缺点是可以减轻的:

*   这让人筋疲力尽:我做结对编程的第一周，我筋疲力尽地离开了办公室，这并不是因为我是一个软弱的人，我公司的每个人都提到这很常见。花一整天和另一个人说话，完全专注于你的工作(因为分心比你一个人工作时更少)会让你的大脑在一天结束时被耗尽。然而，我们通常很快适应新的环境，三四个星期后你就习惯了。此外，强烈建议休息，我们已经开始采用[番茄工作法](http://pomodorotechnique.com/)，它被证明非常有用，可以让我们集中注意力，减少疲劳
*   键盘和鼠标碰撞:在某些情况下，理论上不应该触摸键盘和鼠标的人，在不使用设备的情况下，无法避免这样做来评论一些复杂的事情。大多数情况下，我们会无意识地控制局面，这对另一方来说可能有点烦人，但这种情况很难避免。当它发生时，如果你被打断，我们能做的最好的事情就是幽默地对待它，如果你想打断，试着礼貌地提醒(我能暂时接管吗？)
*   搜索某些东西/谷歌搜索有点尴尬:当我们陷入困境，我们去谷歌寻找解决方案时，我注意到每个人都随机阅读内容，所以我们实际上遵循着两条不同的思路。从一个角度来看，这可能是有益的，因为也许我们更快地找到解决方案，但很多时候我认为如果我单独做，我会做得更舒服。当这种情况发生时，建议使用两台不同的计算机进行并行调查是完全合理的
*   性格冲突:每个人都有不同的性格，如果有分歧，长时间在一起工作会导致关系紧张。我经历过这种情况，与客户开发人员合作比与我公司的同事合作多，因为我们的招聘过程非常注重为我们的工作方式找到合适的人。当你结对的时候，耐心是非常重要的，你永远不应该对另一方无礼
*   脱离:如果你觉得你正在脱离你正在做的任务，或者这种情况发生在另一个任务上，你能做的最好的事情就是提出一个中断。我们在办公室里放了乒乓球桌，方便我们有适当的休息时间，因为我们完全脱离了我们正在做的打乒乓球的任务

## 结论

我不知道这篇文章是否会说服任何人进行结对编程，但至少我希望现在已经很清楚为什么结对编程是如此值得推荐的实践了。如果想了解更多，可以看看肯特·贝克的[极限编程讲解](https://www.amazon.com/Extreme-Programming-Explained-Embrace-Change/dp/0321278658/ref=sr_1_1?ie=UTF8&qid=1478459473&sr=8-1)。