# 软件领导价值观

> 原文：<https://dev.to/monknomo/software-leadership-values>

*[原发表于我的博客](http://www.gunnargissel.com/pages/software-leadership-values.html)*

知道你的价值观是什么很重要，所以当你做决定时，你有一套广泛的原则来做判断。公共问责在鼓励人们坚持自己的价值观方面也非常有用。

我已经写下了 11 个我认为对制作好软件很重要的价值观，并把它们公布出来，这样我就有一个参考来回顾，这样其他人都知道我来自哪里，并且可以告诉我，我是否甚至没有遵循我自己信奉的价值观。

*   顾客是第一位的，但我们必须善待未来的自己
*   [支持团队成员第二](#supportTeam)
*   [人员、想法和工具的多样性造就了更好的软件](#diversity)
*   [人比规则或正确更重要](#peopleMatter)
*   仅仅因为它不是一个 bug，并不意味着它不是一个问题
*   协作远比孤军奋战更有力量
*   [每个人都应该不断学习，并不断分享他们所学到的东西](#constantLearning)
*   我们应该让团队快乐最大化，而不是花太多时间去最小化
*   我们应该为每项工作使用最好的工具
*   [系统比工具更强大，应该优先选择](#preferSystems)
*   [制定计划，但要迅速做出反应](#planResponsively)

## 顾客至上，但我们必须善待未来的自己

[返回列表](#top)

没有顾客，你就不会工作。这使他们成为第一——他们是你在这里的原因。然而，顾客并不总是对的。他们知道他们的问题是什么，但他们可能不知道解决方案是什么。你是他们咨询的专家。引导他们找到对他们和他们的问题最好的解决方案。

我的团队有一个客户对数据库实现细节有强烈的意见。她是一个熟练的查询者和商业专家，但她不是一个开发者。她通常想知道数据库中的人是否符合某一类别。每次请求都会是“你能给数据库添加一个‘在这个类别中’的标志吗？”。通常，这些标志实际上是指示开始或结束日期列是否为空的布尔检查。仅仅盲目地添加标志就给其他过程中的簿记增加了一件事情，并且为新的和有趣的错误创建了一个实质性的领域。

让她做好自己的工作是我们的首要任务，但是善待我们未来的自己需要我们理解她的问题，这样我们才能创造一个不妨碍解决未来问题的解决方案。我们没有添加标志，而是给了她看起来具有所需标志的视图和报告，但是使用了现有的数据。

## 支持团队成员第二

[返回列表](#top)

支持客户可能是第一位的，但是团队是第二位的。团队的每个成员都应该支持其他成员。团队需要和睦相处以支持客户。

这就像在某人休假时代替他，或者交换任务以更好地平衡工作量一样简单。这也意味着在出现问题时支持你团队成员。永远不要把你的团队成员推到公共汽车下或开始政治迫害——即使你的客户是经理要求这样做。

几年前，当我还是一名初级开发人员时，我的团队有一个强大的 Ant 脚本来拆除和重建测试数据库。该脚本使用两个属性文件来确定指向哪个数据库。在正常的开发过程中，出于某种原因，我将脚本指向了产品——也许我正在查询数据，也许我正在现场修复一个问题；我忘记了。我改变了思路，开始进行一些数据库开发。写完 create 语句后，我烧毁了数据库。生产数据库。

幸运的是，团队是支持的。他们帮助解决了即时中断，并指导我如何清理我在生产中造成的混乱。没有指责，没有责备，只是一个很好的教训。这不是真正的考验。真正的测试是我立即对我们的培训应用程序做了同样的事情。这通常不是一个生产应用程序，而是一个为用户做演示的地方。我不小心在一次大型演示前毁了它。

更幸运的是，团队仍然给予支持。我应用了我刚刚从之前的清理中学到的经验，并在演示时及时启动和运行了培训系统。我们将这一系列的错误作为学习的经验，并从日常的开发控制中去掉了红色的大按钮。

## 多样化的人、想法和工具造就更好的软件

[返回列表](#top)

我什么都不知道；没人知道。和我有相同背景的人一般都知道和我相似的事情。不同背景的人知道的东西不一样。对一个团队来说，这太棒了！我可以依靠我的团队成员来弥补我的不足，他们也可以依靠我来弥补他们的不足。组成你的软件的想法和工具的集合也是如此。

马赛克是更好的艺术品——每一个独立的部分组成了一个伟大的整体。[夏加尔的*四季*T3】比](https://en.wikipedia.org/wiki/Four_Seasons_(Chagall))[马列维奇的*黑方*T7】](https://en.wikipedia.org/wiki/Black_Square_(painting))

麦肯锡&公司表示，多元化的公司比非多元化的公司表现出色 35%!

确保应用程序对每个人都有用是一件好事——尤其是当它们是法律要求的联邦政府应用程序时。确保良好的可访问性并不太难，但是有很多活动的部分和事情需要考虑。我设计了一系列图标来表示状态。其中两个是绿色和红色圆圈，分别表示“已签入”和“已签出”。一位色盲同事指出，对于色盲来说，这些图标很难区分。我采纳了同事的反馈，将“已签出”改为黑色方块，并为两者都添加了工具提示，提高了应用程序的可访问性。

## 人比规则或正确更重要

[返回列表](#top)

与你一起工作的人和你服务的人比严格遵守规则和渴望“正确”更重要。团队成员、客户和用户之间的关系是一个无形的系统，它创造了企业和软件。为了遵守次要规则或技术上的正确性而破坏这些关系，会对未来造成巨大的伤害。

这并不是说为了关系可以打破所有的规则；必须努力区分永远不要越过的“明线”规则和“花椰菜”规则——大多数情况下是个好主意，但不是所有时候都是。关键是要认识到，规则是人类创造的，是为我们服务的，而不是相反。

在我的工作中，有一条规则，日志是不可磨灭的-用墨水写的。当船在做的时候，它们应该是船在做什么的记录。有时，由于软件或 peopleware 故障，会出现错误。也许船记录了它在做什么，但是在错误的月份。删除他们的条目是“违反规则”的，但规则的意图是确保高水平的数据质量。为了遵守规则而保存垃圾数据违背了规则的意图，所以我在需要的时候违反了这条规则(在与利益相关者和受影响的当事人进行适当的协商之后)。

其他规则是如此的基本，以至于它们永远不能被违反。我为各种企业处理大量“机密商业信息”。在任何情况下，我都不会把这些信息透露给不是已经为 NOAA 工作的人。甚至对产生信息的企业也不知道——他们应该已经知道了！

## 仅仅因为它不是一个 bug，并不意味着它不是一个问题

[返回列表](#top)

每个开发人员的工作都应该包含一定量的用户支持。不要太多；这是浪费昂贵的时间，但足以确保开发人员随时注意，听到人们真正在说什么。大多数问题并不是真正的软件错误，这是很常见的——系统按照设计运行。如果用户总是遇到同样的问题，开发人员应该试着理解*为什么*他们会遇到这个问题，以及他们能做些什么来帮助他们。通常，少量改变工作流程、行为或演示会导致“不是 bug”支持问题消失。

我参与的一个项目支持政府要求的年度流程。每年，在这个过程开始的时候，咨询台都会接到不知如何开始这个过程的电话。服务台很小，所以我在服务台忙的时候帮助用户。当我和用户一起工作时，我注意到一个趋势:

按照设计，这个程序只允许用户选择有效的选项。用户按下的任何按钮都会起作用，是一个不错的选择。这还不够——用户缺乏信心自己选择按哪个按钮。

在第二年的支持证实问题不仅仅是用户对一个全新的程序有困难之后，我坐下来思考发生了什么。我意识到用户需要一点信心来开始。我用创建了一个向导，引导用户完成涉众的首选工作流程。第二年，关于入门的支持电话降到了零。

## 协作远比孤军奋战更有力量

“每个人都知道”协作是好的，有强大的结果。开发人员很容易专注于手头的问题而放弃合作。

我数不清有多少次和别人一起工作，分享想法、技术和观点，创造出了比任何一个人单独工作都要好得多的东西。有时协作感觉更慢，因为沟通有一定的开销，即使与一个小团队协作也需要大量的会议、配对、电话、电子邮件和聊天。我认为这一切都是值得的，因为当事情开始交付时，它们 a)整体上更好，b)被不止一个人理解。

我认为合作不仅是弥补我的盲点和缺点的一种方式，也是在培训、维护和应急响应方面的节约。当我是唯一的开发者时，我通常是唯一知道我开发的东西如何工作的人。这意味着我要负责未来的所有功能、所有维护，而且当出现问题时，是我的手机在响。协作围绕*自动传播知识和工作量。*

## 每个人都应该不断学习，不断分享所学

[返回列表](#top)

程序员必须不断学习，这是他们工作的主要职能。编程是创造工具来解决未解决问题的学科；如果问题解决了，人们就会购买现成的解决方案。解决未解决的问题需要一定的学习。

建立一个有效使用编程的组织的关键是确保程序员不会把这些知识锁在他们的脑袋里。他们*需要*来分享，不仅如此，开发者通常*喜欢*来分享。

在我的发展道路上，我很幸运；我工作过的组织非常支持学习和成长的培训和资源。当我开始在 NOAA 工作时，开发人员可以参加每年两次的外地会议，条件是他们回来后要展示他们学到的东西。从那以后，我们每周四都有一个固定的时间来演示我们学到的技术。我们的目标是让分享学习变得如此正常，以至于令人厌烦。

## 我们应该让团队快乐最大化，而不是花很多时间去最小化

[返回列表](#top)

两个选项大致相等的决策点应该选择增加团队快乐的选项。然而，做出一个决定比做出绝对“最好”的决定更重要。当花太多时间在选择上犹豫不决时，团队和他们的士气都会受到影响。

领导力的一部分是注意到团队何时不能做出自己的决定，并为他们做出决定。这可能不是一个帝国的决定，“你应该做选项 A，只有选项 A”，而是一个外交，“我们已经仔细评估了选项 A 和 b。他们都是强有力的竞争者，但基于胡说，我们将暂时使用选项 A”。当团队感到有明确的方向和指导时，他们会更开心。

我职业生涯的大部分时间都在主管之间的职位上工作。我的主管辞职了，调走了，升职了，去世了。不管是什么原因，代理主管通常不能担任全职永久主管的角色，因为他们知道他们很快就会回到他们与团队的旧关系中。结果是，我花了很多时间与团队成员一起参与协作决策。

我们有一个坚实的团队，通常我们可以达成一致的决定，或者至少没有任何麻烦的一致决定过程。我们有无法解决的分歧的时候是友好的，但没有指定的领导者来打破这种关系，他们让我们陷入困境。一个能够带领团队克服这些分歧的领导者是有价值的。

## 我们应该为每项工作使用最好的工具

[返回列表](#top)

组织内的开发人员必须能够使用最好的工具来完成手头的工作。他们不能被限制只能使用组织的当前工具集。即便如此，“最佳”应该包括对工具是否适合团队的诚实评估。如果 Go 有最好的工具，但是你的团队都是 Javascript 程序员，你可能想去“Javascript 程序员能用的最好的工具”。

我和一个团队一起工作，他们做大量的 Java 和 PL/SQL。我们主要做网络应用，所以我们的产品主要是耳朵和战争。在不太遥远的过去，我们用来制作耳朵和战争的工具是 ant 和 Eclipse。我们将依赖项的 zip 文件保存在共享的网络驱动器上，并保存了大量关于如何设置和配置开发环境的 wiki 页面。

从某个角度来看，很明显，改变依赖关系，引进新的开发人员是我们最大的痛苦。我们开始将 zip 文件放入源代码控制中，并在灯泡亮起时编写脚本来提取和版本化我们的依赖项 zip 文件:

**我们可以改变我们的工具**

我们换成了 Maven，开始运行内部 Nexus 服务器，再也没有回头。这是一座相当大的山，但意识到我们应该使用有助于我们工作的工具，而不是对抗工具，这是一个改变游戏规则的启示。

## 系统比工具更有力量，应该优先选择

[返回列表](#top)

我知道我刚刚说过，使用最好的工具来完成工作是我的核心软件领导价值观之一。这是另一面

软件开发人员热爱工具，或者至少他们使用很多工具。但是！工具需要维护和开发人员的干预。

系统，尤其是自动化系统，需要更少的维护和更少的直接开发干预。系统通常由多种工具组成。开发人员创建的系统越多，他们自己和团队的力量就越大。

基于工具的工作流缺乏一个要求开发人员掌握所有工具和用法的系统，这分散了开发人员的力量。当考虑到与团队合作时，问题就变得复杂了。团队成员在如何使用工具上看似微小的差异会产生奇怪的问题，而保持所有团队成员工具使用同步的开销是相当大的。

在 NOAA，我从事两个不同的项目。一个依赖于 Maven、Jenkins 和一个持续的集成+一个频繁的(虽然还不是持续的)部署过程。另一个依赖于 Ant，一堆 bash 脚本， [Druid](https://sourceforge.net/projects/druid/?source=directory) ，dbUnit，真的比我想列出的还要多。

第一个项目有很好的记录过程，其中最常见的相当于“按下按钮，观察结果，以这些方式响应”。第二个项目有很多很多的步骤，关于使用每个工具的顺序，把每个工具放在哪个目录，以及构建产品必须复制到哪里，等等。

第一个项目不仅效率更高，而且工作起来更愉快，并且一直在积极地从第二个项目中分流开发人员。这一切都归功于一些允许开发者开发的系统，而不是从维基中复制粘贴程序到众多工具中，并在电话中争论谁在维基中错过了一步。

## 计划周全，却能迅速做出反应

[返回列表](#top)

规划是成功完成项目的关键。应该制定计划、设计、里程碑等等，但必须具有可塑性。僵化的计划是没有弹性的——计划必须能够适应变化的事实和前提。

我在职业生涯中一直很幸运。我的利益相关者总是认识到弹性必须融入到计划中。不仅时间表可能会推迟，功能需求也可能会改变，或者出现紧急情况。

我们经常开发系统来执行尚不存在的法规。取决于房子的监管方，我们可能已经实施了与法规最终版本不匹配的系统或者法规从未正式通过，所以系统被搁置。领导一个移动的目标，就像未完成的法规一样，需要一个包含由外部事件触发的关键决策点的计划。某些事件会引发诸如“我们还要继续下去吗？”或者“为了达到生效日期，我们可以放弃什么？”

具有这种灵活性的构建计划对于开发人员和利益相关者来说都是一种极大的解脱。

如果你喜欢我的价值观，[来我的博客看看更多关于制作软件的内容](http://www.gunnargissel.com)

## 学分

*感谢[马克·弗里斯](https://www.flickr.com/photos/freetheimage/)带来的[罗宾！](https://flic.kr/p/rR2UWM)*