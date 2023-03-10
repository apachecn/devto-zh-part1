# 分析 Google Play，寻找一个有利可图的应用创意——第二部分:免费增值游戏

> 原文：<https://dev.to/alexhyettdev/analysing-google-play-to-find-a-profitable-app-idea-part-2-freemium-games-4nh0>

在我之前的文章中，a 给出了一些一年赚几百万甚至几十亿的应用程序的例子。毫不奇怪，要达到这种盈利水平，你需要数百万的下载量。那么我们如何发现如何做一个盈利的 app 呢？第一件事是看看那些已经做过的人，并找出他们是如何做到的。

我通过查看 Google Play 上的热门应用开始了我的探索，试图找到下载最多的游戏。我将重点关注 Google Play，因为与苹果不同的是，它给出了一个应用程序的下载次数。

你可能会认为找出下载次数最多的应用程序很容易。谷歌向用户展示了几个排行榜。然而，大多数排行榜似乎是基于趋势，而不是下载总数。

## 热门游戏排行榜

[![Top Games](img/aebab2bf75a071cd6570ec7024e29ea6.png)T2】](///static/8ffea77de998646c3c358a4d4c1d59b7/77800/top-games.png)

上面的截图拍摄于 2015 年 4 月 9 日，显示了“热门游戏”排行榜中的前 10 款游戏。谷歌的顶级游戏算法显然没有使用总下载量作为衡量标准，而是必须基于短时间内的下载量。让我们来看看这些游戏的下载量。

1.  [行尸走肉:生存之路](https://play.google.com/store/apps/details?id=com.scopely.headshot)–100 万–500 万次下载
2.  [Luckyo 赌场和免费老虎机](https://play.google.com/store/apps/details?id=com.aemobile.games.casino)–500，000–1，000，000 次下载
3.  烹饪妈妈让我们做饭！–500 万–1000 万次下载
4.  [轻快的车轮](https://play.google.com/store/apps/details?id=com.tottygames.flippywheels)–100 万–500 万次下载
5.  [阿尔法竞技场的英雄](https://play.google.com/store/apps/details?id=com.gamebros.swordsoul.uk)–50，000–100，000 次下载
6.  [美人鱼狂热](https://play.google.com/store/apps/details?id=com.sxiaoao.meirenyu.english)–10 万–50 万次下载
7.  [地铁冲浪者](https://play.google.com/store/apps/details?id=com.kiloo.subwaysurf)–5 亿–10 亿次下载
8.  答对了！–500 万–1000 万次下载
9.  佐蒙:英雄之路–10，000–50，000 次下载
10.  [硬币推土机:海盗](https://play.google.com/store/apps/details?id=com.gamecircus.CoinDozerPirates)–100 万–500 万次下载

上面下载量最多的游戏是地铁冲浪者，下载量惊人地超过 5 亿次，但考虑到它在这个列表中只排在第七位，它并不能帮助我们找到有史以来下载量最多的游戏。

免费游戏的另一张图表是票房排行榜。现在，这可能意味着下载量最多的游戏，赚钱最多的游戏或者其他未知的谷歌算法。让我们来看看这张图表中的前 10 名。

## 票房排行榜

随着《部落冲突》和《糖果粉碎传奇》等游戏的上市，这看起来更有希望。然而，当你看下载总数时，游戏并没有按下载顺序排列。

1.  [部落冲突](https://play.google.com/store/apps/details?id=com.supercell.clashofclans)–1 亿–5 亿次下载
2.  [战争游戏-火之时代](https://play.google.com/store/apps/details?id=com.machinezone.gow)-1000 万-5000 万次下载
3.  [糖果粉碎传奇](https://play.google.com/store/apps/details?id=com.king.candycrushsaga)–1 亿–5 亿次下载
4.  [糖果汽水](https://play.google.com/store/apps/details?id=com.king.candycrushsodasaga)–1 亿–5 亿次下载
5.  [王者冲突](https://play.google.com/store/apps/details?id=com.hcg.cok.gp)–1000 万–5000 万次下载
6.  [防辐射掩体](https://play.google.com/store/apps/details?id=com.bethsoft.falloutshelter)–500 万–1000 万次下载
7.  [Boom Beach](https://play.google.com/store/apps/details?id=com.supercell.boombeach)–1000 万–5000 万次下载
8.  [农场英雄传奇](https://play.google.com/store/apps/details?id=com.king.farmheroessaga)–1 亿–5 亿次下载
9.  [海盗王](https://play.google.com/store/apps/details?id=com.jellybtn.cashkingmobile)–1000 万–5000 万次下载
10.  [快乐日](https://play.google.com/store/apps/details?id=com.supercell.hayday)–1 亿–5 亿次下载

它们不仅没有按下载量排序，我们在上图中看到的下载量超过 5 亿的 Subway Surfers 甚至不在列表中，愤怒的小鸟或其他一些知名游戏也不在列表中。

当我遇到 PlayDrone 时，我正准备放弃使用 Google Play 进行任何有用分析的希望。PlayDrone 是哥伦比亚大学完成的一个项目，他写了一篇关于[的论文，是对 Google Play](http://systems.cs.columbia.edu/archive/pub/2014/06/a-measurement-study-of-google-play/) 的测量研究。如果你有时间阅读的话，这是一篇有趣的论文，它对流行应用所使用的框架提供了一些见解。

该团队使用了许多电脑和 50 个谷歌账户来分析应用商店中的所有 140 万个应用程序，下载了所有免费的 APK 软件，并对它们进行反编译。当他们发现大量应用程序通过在源代码中包含用户名和密码而造成安全问题时，这也成为了新闻。

如果你想看的话，代码都在 [GitHub](https://github.com/nviennot/playdrone) 上，但是他们建议你不要运行它，因为代码还没有准备好投入生产，并且通过阅读这篇论文，该团队使用了许多黑客技术来收集数据。

幸运的是，Archive.org 的人已经运行 PlayDrone 几天了，并提供了可供下载的数据。最新的快照来自 2014 年 10 月 31 日，因此有点旧，但适合我进行分析。

该快照由一个 735MB 的 JSON 文件组成，其中包含大量的详细信息，如名称、下载和星级。还有一个链接指向另一个 JSON 文件，其中包含每个应用程序的更多数据，如应用程序或任何应用程序购买的星级和价格的细分。如果你想做一些研究，所有这些都是很好的数据。

显然，试图在大型文本文件上运行查询不是特别有用。作为一名工程师，我编写了一个快速控制台应用程序，将数据导入 SQL Server Express 数据库，以便进行分析。如果你感兴趣，我会在 GitHub 上和本系列的最后一篇文章一起分享这个的源代码。我运行了一个查询，以获得下载量超过 5000 万的所有游戏的列表，这里是按下载量和星级排序的前 20 名的列表(请记住数据来自 2014 年 10 月 31 日)。

1.  [卑鄙的我](https://play.google.com/store/apps/details?id=com.gameloft.android.ANMP.GloftDMHM)–1 亿次下载
2.  [地铁冲浪者](https://play.google.com/store/apps/details?id=com.kiloo.subwaysurf)–1 亿次下载
3.  [飙车](https://play.google.com/store/apps/details?id=com.creativemobile.DragRacing)–1 亿次下载
4.  [神庙逃亡](https://play.google.com/store/apps/details?id=com.imangi.templerun)–1 亿次下载
5.  《愤怒的小鸟》–1 亿次下载
6.  [爬山比赛](https://play.google.com/store/apps/details?id=com.fingersoft.hillclimb)–100，000，000 次下载
7.  《愤怒的小鸟》里约版–1 亿次下载
8.  [我会说话的汤姆](https://play.google.com/store/apps/details?id=com.outfit7.mytalkingtomfree)–1 亿次下载
9.  《愤怒的小鸟》第四季–1 亿次下载
10.  [Pou](https://play.google.com/store/apps/details?id=me.pou.app)–1 亿次下载
11.  [糖果粉碎传奇](https://play.google.com/store/apps/details?id=com.king.candycrushsaga)–1 亿次下载
12.  [免费水果忍者](https://play.google.com/store/apps/details?id=com.halfbrick.fruitninjafree)–1 亿次下载
13.  [Jetpack Joyride](https://play.google.com/store/apps/details?id=com.halfbrick.jetpackjoyride)–1 亿次下载
14.  [神庙逃亡 2](https://play.google.com/store/apps/details?id=com.imangi.templerun2)–1 亿次下载
15.  [台球职业赛](https://play.google.com/store/apps/details?id=com.forthblue.pool)–1 亿次下载
16.  [射击泡泡豪华版](https://play.google.com/store/apps/details?id=com.shootbubble.bubbledexlue)–1 亿次下载
17.  [部落冲突](https://play.google.com/store/apps/details?id=com.supercell.clashofclans)–5000 万次下载
18.  [珠宝之星](https://play.google.com/store/apps/details?id=com.game.JewelsStar)–5000 万次下载
19.  [驾驶博士](https://play.google.com/store/apps/details?id=com.ansangha.drdriving)–5000 万次下载
20.  [免费自行车赛–顶级免费游戏](https://play.google.com/store/apps/details?id=com.topfreegames.bikeracefreeworld)–50，000，000 次下载

因此，有了 PlayDrone 的数据，我们现在有了 Google Play 上下载最多的游戏的列表，我们可以开始了解哪些类型最受欢迎。

从上面的列表中，看看热门游戏，你可以将大部分热门游戏归为少数几个游戏类型。

## 无限“奔跑者”

从上面的列表中可以明显看出，每个人都喜欢无限跑步游戏，卑鄙的我，地铁冲浪者，神庙逃亡游戏，爬山赛车和 Jetpack Joyride 都让数百万用户有了一次机会。

不需要严格意义上的跑步游戏。这些游戏的关键是它们有多容易玩。他们需要很少的理解就可以开始，但需要少量的技能才能在游戏中走得更远。它们只需要一只手就可以玩，而且你想玩多久就玩多久，所以在公共汽车上、等待约会或坐在厕所里都是完美的！其他游戏如 [Doodle Jump](https://play.google.com/store/apps/details?id=com.lima.doodlejump) 、 [Timberman](https://play.google.com/store/apps/details?id=com.dm.timber) 和 Flappy Bird 都使用这种无休止的游戏模式(并且都受到了疯狂的欢迎)。

## 赛车/驾驶/运动

赛车和驾驶游戏也很受欢迎，在这个列表中有几个例子，如飙车，驾驶博士和自由自行车赛。大多数最受欢迎的赛车游戏通常是 3D 的，有很好的图形，所以对于一个单人开发者来说可能是遥不可及的，但是像免费自行车赛这样的游戏仍然非常受欢迎。你是否认为赛车是一项运动有待讨论，但你也会发现像台球和足球这样的游戏在排行榜上名列前茅。

## 物理谜题

愤怒的小鸟和它的不同版本，主宰了这个领域。与无限跑游戏一样，这些游戏非常容易玩，几乎可以在任何地方玩。需要一些技巧来让这些游戏更吸引人，并鼓励用户与朋友分享他们的分数。

## 数码宠物

我只能假设这是下一代电子鸡给那些太年轻而不记得它们的人。我会说话的汤姆和名字恰如其分的普名列榜首。鼓励用户照顾他们的数码宠物，给它们喂食，给它们洗澡，让它们上床睡觉。我们都知道这些电子宠物小时候是多么容易上瘾。如果你看一看由 [Outfit7](https://play.google.com/store/apps/developer?id=Outfit7) (他制作了会说话的汤姆)制作的游戏，你会发现各种各样的数字动物可以被当作宠物。

## 匹配 3

最赚钱的游戏之一应该是《糖果粉碎传奇》(2013 年收入 18.8 亿美元)。流行的甜蜜粉碎游戏由国王属于比赛 3 流派，其中有很多。游戏的目的很简单，匹配 3 个或更多的东西，要么清除棋盘，要么在一定数量的移动中获得足够的分数。这些类型的游戏已经流行多年了，还记得《宝石迷阵》吗？在上面的前 20 名中有 3 个这种类型的游戏，糖果粉碎传奇，宝石之星和射击泡泡豪华。最后一个是基于经典的泡泡射击游戏，但本质上仍然遵循第三场比赛的模式。

## 策略

接下来的高排名游戏是策略游戏，《部落冲突》明显领先。我记得连续玩了几个小时的帝国时代，这种类似的游戏让用户不停地玩。这些游戏通常以战争为基础，目的是建立你的军队来击败你的对手。

## 其他

有些游戏打破常规，不属于某一特定类别。我认为水果忍者属于这一类。据我所知，这是第一款采用触摸界面的游戏。结合漂亮的图形和简单的概念使这款游戏大受欢迎。如果你能想出一个全新的游戏方式，这是一个风险，但它可以做得很好。

如果你能从上述类别中拿出一款像样的游戏，它很可能会获得大量下载。你可能不会像 Flappy Bird 一样一天赚 5 万美元，但如果你营销得当，它肯定会受欢迎。

在我的下一篇文章中，我将使用我的 PlayDrone 数据库来寻找有史以来下载量最大的付费游戏(潜行巅峰:《我的世界》！)以及是什么让它们如此受欢迎。