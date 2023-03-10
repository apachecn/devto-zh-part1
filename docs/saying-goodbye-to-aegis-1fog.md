# 向神盾说再见

> 原文：<https://dev.to/highperformancecoder/saying-goodbye-to-aegis-1fog>

时隔近 16 年，我现在要对宙斯盾源代码管理系统([http://aegis.sf.net](http://aegis.sf.net))说再见了。宙斯盾在它的日子里，领先于它的时代很多年。但现在，随着《宙斯盾》的作者去世，只有少数坚定的人在推广和维护《宙斯盾》，是时候寻找一个替代者了。在愤怒地使用 git 和 github 超过 18 个月后，我想我终于有了一个能够胜任工作的 SCM。另一方面，Github 庞大的开发者社区和 fork/pull 请求模型意味着人们更有可能做出贡献。虽然 Aegis 有一些类似的东西，但现实是很少有人会费心去下载和安装 Aegis，所以你只能实现结合多个 SCMs 的笨重的工作流。异构存储库不止一次地导致代码回归。

最大的障碍是如何处理持续集成，这是 Aegis 从一开始就有的特性。经过一段相当长的学习曲线后，我找到了一个关于 TravisCI 的解决方案，它与 Github 集成得非常好。然后我需要一些东西来代替我在 Aegis 中的版本控制工作流。在研究 Gitflow 之后，我意识到它与我使用 Aegis 所做的非常接近，所以我使用脚本“makeRelease.sh”实现了一个版本控制工作流，该脚本使用 git 标签特性来添加版本号，并向 Makefile 添加了一个 dist 目标来创建特定版本的干净 tarballs。

不过，我会稍微改变一下。而 Aegis 分支编号与 delta 编号没有关系，所以分支 ecolab.5.32 实际上是 ecolab release 5 之上的增量工作。D29，使用我的新工作流程，分支和增量将是相同的。版本 5.32.1 将是 ecolab.5.32 的增量测试版。此外，为了表明新系统已经到位，Aegis 的 delta 编号(最终位置的 D)已经消失，版本将完全是数字。

你可以在 github 的仓库里查看新的东西，https://github.com/highperformancecoder/minsky 的[和 https://github.com/highperformancecoder/ecolab 的【T2 的](https://github.com/highperformancecoder/minsky)。