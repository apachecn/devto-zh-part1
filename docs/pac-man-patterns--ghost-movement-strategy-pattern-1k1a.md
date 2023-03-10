# 吃豆人模式—幽灵行动(策略模式)

> 原文：<https://dev.to/code2bits/pac-man-patterns--ghost-movement-strategy-pattern-1k1a>

[![](img/dd09f174b65ef69d861feb996da5efb1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AEm5P_Nq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/0%2A2Q3S37cKEOHRCmAD.jpg)

当我阅读 Jamey Pittman 写的一篇关于吃豆人的文章时，我想到了用吃豆人来说明设计模式的用法。他的文章是关于经典的 Namco 游戏《吃豆人》中的设计和人工智能课程。我真的对一款在当时看起来如此简单的游戏在设计和人工智能方面的复杂性感到惊讶。然后，我决定撰写一系列文章，阐述如何使用设计模式来实现游戏《吃豆人》中的一些复杂设计问题。

“吃豆人模式”系列的第一篇文章将关注四个幽灵在不同游戏模式下的不同行为。我们还将看看策略模式如何帮助设计不同的运动行为。

《吃豆人》是一款街机游戏，于 1980 年首次发行。玩家通过迷宫驾驶吃豆人，并收集所有的点(吃豆点)以完成这一阶段。吃豆人在游戏中被四个幽灵追逐，他们的主要目标是杀死他。四个幽灵，Blinky，Pinky，inky 和 Clyde，每个都有不同的行为，取决于幽灵的模式。幽灵在游戏过程中改变模式，从分散到迷宫的角落，到追逐 Pac-Man，当 Pac-Man 捡起一个能量球时受到惊吓。

[![](img/243861a51ba487f20bd39b993290515c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2Uo1Z-4Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/0%2A1GwJvRgVTwygmEfl.jpg)

本文讨论了 Pac-Man 中幽灵的不同运动和行为，以及它与实现可重用的面向对象软件(也称为设计模式)的关系。

**追逐**

在“追逐”模式中，幽灵们试图找到并抓住吃豆人。四个幽灵在追逐吃豆人时都有独特的行为。红色幽灵 Blinky 在追逐吃豆人的时候攻击性很强，一旦找到吃豆人，它就会跟着吃豆人。粉红鬼 Pinky 将试图伏击吃豆人，试图走到他面前，切断他的去路。青色幽灵会巡逻一个区域，在这种模式下很难预测。橙色幽灵克莱德以一种随机的方式移动，似乎远离了吃豆人。

[![](img/d444d453aa11c56195407945ea3f866a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tMDeCSH4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/0%2ACoMAiBNHxshA7HIw.png)

**散开**

在“分散”模式下，幽灵将停止追逐吃豆人，每个人都将进入各自的角落几秒钟。红色幽灵 Blinky 向右上角移动，而粉色幽灵 Pink 向左上角移动。青色幽灵 Inky 向左下角移动，橙色幽灵 Clyde 向左下角移动。这种模式只持续几秒钟，然后变回“追逐”模式。

[![](img/10087d8f664e4f0074d164c623302b8c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Rvtrl2Gc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/0%2AzNDnbLILf0WwS3co.png)

**惊恐**

当吃豆人在迷宫里吃了一个增能剂时，就会出现“害怕”的模式。迷宫中有四个增能器，所有四个鬼魂都会改变模式。鬼魂变成深蓝色，在迷宫中四处游荡，非常脆弱。在他们回到分散或追逐模式之前，他们会闪烁片刻。

[![](img/9b6ca0d8995bb10013b9b8d21a58b443.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AY3cmOy1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/0%2AXDKqZ9aXMYhUmXxc.png)

因此，为了总结幽灵的移动要求，下表说明了移动的类型，以及在这些类型的移动中各个幽灵的行为。

鬼名追撒惊吓 Blinky(红色)好斗右上角游荡 Pinky(粉色)埋伏左上角游荡 inky(青色)巡逻右下角游荡 Clyde(橙色)随机左下角游荡

为了实现不同的 ghost 行为，Ghost 类的实例将使用由一个接口表示的行为(ChaseBehaviour、scatter behavior & frightened behaviour)来确保每个行为的不同实现不会在 Ghost 类中实现。

根据策略模式，变化的行为被放入一个单独的类中，以允许您对那些行为进行更改，而不影响保持不变的部分。此外，该模式符合“对接口编程，而不是对实现编程”的设计原则，因此可以将重影的三种模式定义为接口，并且可以在单独的类中实现运动模式的不同实现。

幽灵类有追逐行为、分散行为和恐怖行为。HAS-A 关系指的是类的组成。现在有可能以这样一种方式组成 Ghost 类，即每种不同类型的 Ghost 都可以由 3 种运动模式的不同实现组成。

幽灵的追逐行为各不相同，每个部分的实现都被放到一个单独的类中。因此，积极追逐、伏击、巡逻和随机的算法被放在单独的类中。创建一个接口(Chase behaviour)来允许 Ghost 类和 Chase behaviour 的不同实现之间的组合。

下图说明了 Ghost 类的组成和实现以及 ChaseBahaviour 的不同实现:

[![](img/cb84eb7091640fa4fae9eb569a52409b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--W5ns_qpi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/0%2AgxvDPPeGZny9DzhU.png)

*   幽灵类包含了吃豆人游戏中不同幽灵的不同行为。一个鬼魂可以有三种不同的模式:追逐，分散和恐惧。
*   [追逐行为](https://github.com/Code2Bits/Design-Patterns-Java/blob/master/Behavioral%20Patterns/Strategy/Source/src/main/java/com/code2bits/designpatterns/behavioral/strategy/ChaseBehaviour.java) —在吃豆人游戏的追逐模式中，追逐行为接口用于定义不同的幽灵行为。在追逐模式中，鬼魂会有不同的行为和他们的个性有关。
*   chase aggressive—chase aggressive 类包含了吃豆人游戏中幽灵的行为。在追逐模式下，幽灵会积极地追逐你，通常会选择最短的路线，并倾向于跟随你。
*   chase buzz 类包含了吃豆人游戏中幽灵的行为。在追逐模式下，幽灵会试图伏击吃豆人。幽灵倾向于对吃豆人采取更伤人的方式，并产生致命的效果。
*   ChasePatrol 类包含了吃豆人游戏中幽灵的行为。在追逐模式下，默认情况下，幽灵会在他指定的街区巡逻，只有当他足够靠近时才会追逐吃豆人。
*   ChaseRandom 类包含了吃豆人游戏中幽灵的行为。在追逐模式下，幽灵会在棋盘上随意移动，不会造成太大威胁。

重影的分散行为各不相同，每个部分的实现都被放到一个单独的类中。因此，分散到左上角、右上角、左下角和右下角的算法放在不同的类中。创建一个接口(Scatter behaviour)来允许 Ghost 类和分散行为的不同实现之间的组合。

下图说明了 Ghost 类的组成和实现以及 ScatterBahaviour 的不同实现:

[![](img/fb03e18b6610607d9995f0841a6ba60b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--leZWakkC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/0%2A4_IhhGxPSU_MsVBk.png)

*   幽灵类包含了吃豆人游戏中不同幽灵的不同行为。一个鬼魂可以有三种不同的模式:追逐，分散和恐惧。
*   [分散行为](https://github.com/Code2Bits/Design-Patterns-Java/blob/master/Behavioral%20Patterns/Strategy/Source/src/main/java/com/code2bits/designpatterns/behavioral/strategy/ScatterBehaviour.java) —分散行为接口用于在吃豆人游戏的分散模式中定义不同的幽灵行为。在分散模式下，幽灵们放弃追逐，奔向各自的家。
*   ScatterTopLeftCorner—ScatterTopLeftCorner 类包含了吃豆人游戏中幽灵的行为。在分散模式下，幽灵将放弃追逐，使用常规的路径搜索方法前往棋盘的左上角。
*   ScatterTopRightCorner—ScatterTopRightCorner 类包含了吃豆人游戏中幽灵的行为。在分散模式下，幽灵将放弃追逐，使用常规的路径搜索方法前往棋盘的右上角。
*   ScatterBottomLeftCorner—ScatterBottomLeftCorner 类包含了吃豆人游戏中幽灵的行为。在分散模式下，幽灵将放弃追逐，使用常规的路径搜索方法前往棋盘的左下角。
*   ScatterBottomRightCorner—ScatterBottomRightCorner 类包含了吃豆人游戏中幽灵的行为。在分散模式下，幽灵将放弃追逐，使用常规的路径搜索方法前往棋盘的右下角。

幽灵的惊恐行为可能没有变化，但是的实现仍然被移到一个单独的类中。因此，漫游算法放在单独的类中。创建了一个接口(惊吓行为),以允许 Ghost 类和漫游行为的实现之间的组合。

下图说明了 Ghost 类的组成和实现，以及不同的 FrightenedBehaviour 实现:

[![](img/1f52ae0452251515e5fe0cf753aeb83e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d_FeKLNV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/0%2A1dg8YMVXIp6hLu9S.png)

*   幽灵类包含了吃豆人游戏中不同幽灵的不同行为。一个鬼魂可以有三种不同的模式:追逐，分散和恐惧。
*   [惊吓行为](https://github.com/Code2Bits/Design-Patterns-Java/blob/master/Behavioral%20Patterns/Strategy/Source/src/main/java/com/code2bits/designpatterns/behavioral/strategy/FrightenedBehaviour.java) —惊吓行为接口用于定义吃豆人游戏惊吓模式下不同的幽灵行为。在恐惧模式下，鬼魂都会变成深蓝色。
*   这个类包含了吃豆人游戏中一个幽灵的行为。在惊吓模式下，鬼魂会全部变成深蓝色，漫无目的地在迷宫里徘徊几秒钟。

策略模式有助于设计《吃豆人》中鬼魂的不同行为。将策略模式应用于 ghost 运动设计，解决方案变得可重用、可扩展、可维护，并允许在不对代码的其余部分产生重大影响的情况下进行更改。

我希望这篇文章重新点燃你玩吃豆人游戏和实现可重用面向对象软件(也称为设计模式)的美好时光。

*原载于 2018 年 1 月 21 日*[*【www.code2bits.com】*](https://www.code2bits.com/strategy-pattern-pac-man-ghost-movement/)*。*

* * *