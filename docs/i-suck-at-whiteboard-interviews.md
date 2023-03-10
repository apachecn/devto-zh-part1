# 我不擅长白板面试

> 原文：<https://dev.to/paulleclercq/i-suck-at-whiteboard-interviews>

*也刊登在[媒体上](https://medium.com/@polomarcus/i-suck-at-whiteboards-interviews-809e9927d2d6)*
如今我的主要目标是找到我梦寐以求的工作，为什么？仅仅因为我年轻，没有孩子，没有贷款，我认为我有正确的积极的能量和知识去做任何事情，所以为什么不专注于这些工作呢？
由于我写了两篇关于 Spotify API 的博客文章([第一篇](https://medium.com/@polomarcus/analyze-one-year-of-radio-station-songs-aired-with-sql-spark-spotify-and-databricks-835fcf73df6)和[第二篇](https://medium.com/@polomarcus/music-recommendation-service-with-the-spotify-api-spark-mllib-and-databricks-7cde9b16d35d))，我最近在 Spotify 斯德哥尔摩的办公室面试了一个数据工程师的职位。2017 年 3 月第一次视频面试，5 月 31 日最后一次现场面试。

[![Stockholm, Sweden (credit Unsplash)](img/1d22f5565f2257d91bb6ab6dbcadd73c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_i-FuugD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1500/1%2Agwe2Kiq_Z_Z6ECfgyXdO5g.jpeg)

为什么我认为这个职位是一份理想的工作:

*   他们在他们的工程文化上做了很多工作
*   他们的技术博客上的精彩阅读，意味着你可以与最好的工程师之一合作，并向他们学习
*   他们在谷歌云平台上使用托管解决方案，这意味着花更多时间在产品上，花更少时间管理服务器
*   [在 GCP](https://github.com/spotify/scio) 和[的基础上构建高效工作的工具，使用新技术](https://beam.apache.org/get-started/beam-overview/)定义批处理和流数据处理作业

这种工作让你成长为一名工程师。

# 准备

我读过大量的文章，读过[马丁·克莱普曼的《设计数据密集型应用》](https://library.oreilly.com/book/0636920032175/designing-data-intensive-applications/26.xhtml?ref=toc#idm140605782689984)，[霍尔登·卡劳的《高性能火花》](http://shop.oreilly.com/product/0636920046967.do)，尝试了很多新技术，在两次见面会上向[展示了我的第一次演讲](https://www.slideshare.net/PaulLeclercq2/analyze-one-year-of-radio-station-songs-aired-with-spark-sql-spotify-and-databricks)，感谢我感受到的面试压力。

> ![Donald J. Trump profile image](img/93c4b46169814a93d4fa1d6d266ef5c9.png)唐纳德·j·特朗普@ realdonaldtrump![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)你知道的越多，你就越意识到自己有多不知道。如果你已经什么都知道了，你怎么可能发现什么呢？2014 年 6 月 30 日下午 19:09[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=483688700447326209)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=483688700447326209)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=483688700447326209)

<center>*The more you know the less you know, Trump version*</center>

我还参加了一些其他公司的技术面试，以练习我的演示技巧，*嗯……这就是游戏，你也可能足够幸运地找到极好的机会*，在那里我有时会被奇怪地问到我在计算机科学研究期间学到的东西:[如何在散列表中处理冲突](https://stackoverflow.com/questions/4980757/how-do-hashtables-deal-with-collisions/4980797#4980797)，或者关于[链表的练习](https://www.interviewcake.com/concept/python/linked-list?)。我花了很多时间来准备它，它让我成为一名更好的工程师，这种感觉很好。我也尽可能地记录了面试中的一切，以确保在接下来的面试中弥补我的不足。同样为了增强我的信心，就在我现场面试之前，我在斯德哥尔摩国家图书馆花了一整天为第二天的面试做准备。

[![Stockholm, Sweden (credit Unsplash)](img/bc638211991831b18a1fd8ab3e3d4fdb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--y5Pi1thd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2AsN6zs7mGAG2wIi91T1V2nQ.jpeg)

# 现场面试

我在电话中受到了人力资源部的欢迎，他友好地向我解释了将要发生的事情以及我在面试中应该如何表现。在闲聊中，他问我怎么样，我真诚地回答他，我感到压力很大，那是因为我前一天晚上没怎么睡觉，但我真的很兴奋，对这些采访很好奇。

他们使用随和风格的面试来确保你在白板前尽可能表现正常，或者至少表现得最正常。很多微笑，软饮料，洗手间邀请…

## 编程面试和系统设计

当我被问到一些面试官知道的问题时，我总是有这种奇怪的感觉，事实上，当我和一个说法语的人说英语时，我也有同样的感觉，这个人的英语说得比我好。我有一个建议，但我很难遵循，那就是想象你想向一个不在你的团队工作的同事解释你的解决方案，作为一名数据工程师，例如一名前端开发人员:他有潜力理解它，但没有像你一样花太多时间去思考它。我们已经讨论了如何为播放最多的歌曲设计一个实时仪表板，以及如何在保持顺序的同时从列表中删除重复的歌曲。我有一种很棒的感觉，我几乎把所有的事情都做对了，这给了我对其他人的信心。

## 午餐

我有一个小时的休息时间，我以为我会独自度过，所以我带了一本书，但实际上，那天采访我的工程师和我一起吃饭，我真的很喜欢，我觉得自己像一个真正的团队成员，即使只有一个小时。趣闻:有一次我去一家大公司面试，我的两个面试官显然已经吃过饭了，他们不能在我吃午饭的时候离开我，所以我吃了我一生中最奇怪的一顿午饭，两个你不认识的人在看着你吃饭的时候试图闲聊。

## 文化面试

我宁愿进行书面的文化面试，因为有些问题不经过深思熟虑是无法回答的——你给出的最好建议是什么？嗯..良好的..我猜是锻炼和健康饮食吧？。其他问题是什么是团队？什么是成功的？而且你要用具体的例子来回答这些问题。
在回答这些问题时，我会尽量诚实，例如，我说过我需要一个团队来支持我提出的解决方案，否则我无法 100%确定。但是我想修饰答案是很容易的。我的建议是试着这样做，因为这只是面试游戏。
另一个建议是，你应该毫不犹豫地想在哪个团队工作，你给出的每个答案都应该说，我是这个团队的合适人选。要知道这一点，你需要做功课，在 LinkedIn 上寻找人们在他们的团队中实际做什么。

## 案例分析面试

对于他们遇到的实际问题，他们想知道您将如何调试隐藏在监控工具中的问题。这是一次角色扮演式的面试，最后他和两位面试官玩得很开心。他们中的一个知道我已经制造了#1 在线闹钟。当面试官知道你的简介时感觉很好，你可以说这是面试人时显而易见的事情，但这真的很少。你会对我在同一家公司的不同面试中展示我的个人资料的次数感到惊讶，这是我目前感到面试疲劳的原因之一，尽管我喜欢谈论数据工程。有一次在 5 个小时的现场面试中，我必须回答一个在同一天的前一次面试中已经问过我的问题，即使我告诉面试官我已经被问过这个问题，他也建议我:试着给出一个不同的答案。现场面试后，我知道我不会接受那里的任何工作。我给面试官的一些建议是，请保持敏捷，像一个人一样，因为每个面试者都是不同的。

## 结果

在 Spotify 的办公室接受采访 5 个小时后，我感觉好极了，精力充沛，这意味着我度过了美好的时光。我和我到达时遇到的人力资源总结了一天的工作，他非常友好地建议我参观整个办公室:我这辈子从没见过这么多微波炉哈哈
两天后，在发现斯德哥尔摩这个我因为做得太好而要搬到的城市后，我收到了同一位人力资源发来的电子邮件

> 很抱歉，我没能更早地了解现场会议的结果。花费的时间比预期的长。不幸的是，我必须让你知道，我们不会继续与你这个角色。

[![Boy, that hurts](img/298a71a54cc0dddb9c8b4eeff3ef2158.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--osa0ovfv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2A4xR6bhFYug1LMRArIP7N0Q.jpeg)

他还让我通过电话了解每一次面试，这样我就能了解他们的最终答案。非常感谢。总结他们的决定，我倾向于缺乏信心，当我解释我的解决方案时，我没有把握。但我想这就是白板面试的意义所在，看看你如何在没有常用工具的情况下处理一个新问题，适当的时间来检查你的不同解决方案，两个人观察你的所有动作。我很乐意被问到更多关于我提出的解决方案的问题，以表明我可以回答大多数问题。但是为了让我满意，人力资源告诉我所有的面试官都说我很友好，我想这是格里丰多的一个优点？他也给了我希望，我可以在一年内成为一名优秀的运动员

# 我对 5 小时现场面试的看法

现场面试是在从大量简历中被选中后进行的，与人力资源和工程师进行了两次 45 分钟的电话/视频面试，以确保你能胜任他们提供的角色，以及与两名工程师进行了一次 1:30 小时的视频技术面试。如果我们把现场采访的时间加起来，就是 8 个小时。如果我把这与我和新员工共事的经历进行比较，我可以在 30 分钟内判断这个人是否知道如何编码，我是否会和他/她相处得很好，最重要的是，这个人是否对她/他的工作充满热情。 **[我们怎么可能需要 8 个小时才能看到那个](https://medium.com/javascript-scene/tech-hiring-has-always-been-broken-heres-how-i-survived-it-for-decades-b7ac33088de6)？**

虽然我在 Spotify 接受采访时过得很愉快，但我不得不说，有一秒钟我不禁感到沮丧，并认真思考质疑我的专业技能。尤其是当你认为你会得到一份工作时，因为你对你给出的所有答案都很满意，有时甚至感到自豪。另外，当你根据 API 编写了两个展示你的技能的案例。

另一方面，你会如何处理一万名求职者申请一个职位？你必须选择细节，就像 Spotify 对我做的那样，我完全可以理解这一点。

在我看来，现场面试应该是随意的，不需要代码，就像视频面试一样。只有案例研究或架构设计，看看你对不同主题的观点，以及你能给公司带来什么，你面试的整个团队，随后是一顿舒适的晚餐，以确保你能很好地融入团队。

# 试试。

为了完成这篇文章，并鼓励你花适当的时间去申请你的梦想/更好的工作，请看一下[来自《实用发展》的本的这句话](https://dev.to/ben/embrace-how-random-the-programming-interview-is)

> 但是如果你的目标是找到你梦想中的工作——或者只是得到你的第一份工作——那就玩游戏，冒更多的风险。取消你的资格取决于招聘公司，这不是你的工作。有人会成为随机性的恩人，那也可能是你。