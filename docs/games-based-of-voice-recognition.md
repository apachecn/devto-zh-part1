# 基于语音识别的游戏

> 原文：<https://dev.to/plutov/games-based-of-voice-recognition>

[![git-gc](img/d73a8ad7bde5dd0a9f6fe3e96d5489d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qbtJkfwi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/odxsqndctg5qx2hoiuw1.png) 
大家好！

上周六，Wizeline Vietnam 团队组织的使用服务器端 Swift talk 的 Alexa 技能让我深受启发。演讲者做了一个如何在英雄联盟(LoL)中使用 Alex Skill 的演示。这项技能使用户能够使用语音命令在比赛中检索敌方队伍的统计数据。

我带着一个完全基于语音识别构建游戏的想法回家，没有任何界面。过去我有使用谷歌语音 API 和谷歌翻译 API 的经验，所以我决定尝试一下。

当然还有去！

我创建了一个 [PoC 开源项目](https://github.com/plutov/games)来实现一个`20 questions`游戏，一个简单有趣的游戏，你应该通过问最多 20 个问题来猜测一个名词。问题只能用“是”、“否”和“不知道”来回答。演示页面位于[这里](http://pliutau.com/games/)。

目前，我正在使用 webkit 技术进行语音识别，但我计划将其转移到后端，并使用谷歌语音 API。此外，在计划中，我想使用谷歌翻译 API 支持所有可能的语言。

如果你有兴趣，请加入一个项目！

*[我博客里的原帖](http://pliutau.com/voice-recognition-games/)*