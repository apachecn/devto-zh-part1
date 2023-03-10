# 或者:六个小时怎么变成了六个月

> 原文：<https://dev.to/buntine/computerpioneers-or-how-six-hours-turned-into-six-months>

几个月前，我对我的女朋友做了一次即兴评论，大意如下:

> 我希望能够看到一群计算机先驱，他们的规模与他们在计算机科学特定子领域的影响力成正比

当时，这似乎是一个非常简单的项目。我只是抓取一些预先打包好的数据，然后快速生成一个小的循环打包算法。或者，也许我只是利用一些现有的数据可视化框架，如 D3。可能需要几个小时...对吗？

第一个障碍是数据。我可以找到许多该领域成就的数据来源，但没有一样是既可消费又足够完整的。在我找到的资料来源中，所有人都简单地宣称一项成就就是:一项成就。没有人试图提供每项成就的相对影响，这可能是因为这样做的内在风险。我所说的风险是指成就很难(或不可能)真正相互比较的事实。格蕾丝·赫柏的 [FLOW-MATIC](https://en.wikipedia.org/wiki/FLOW-MATIC) 编译器比罗宾·米尔纳的 [ML](https://en.wikipedia.org/wiki/ML_(programming_language)) 更有影响力吗？如果没有另一个，其中一个会发生吗？哦不，这太难了！无论是哪种情况，我决定我需要以我自己的格式收集我自己的数据，并希望最终结果足够公平和足够准确。

三个小时后，我来到了先锋 4 号。太好了，我想，这需要一段时间...虽然，我必须承认，我的一部分是默默的有点兴奋，因为我知道手动操作将迫使我实际上学习一吨有趣的东西，关于在我的领域的巨人过去的成就。作为一个业余历史爱好者，这样的挑战对我来说并不完全无趣！

但现在我们来看看真正的包装。如果你不确定，当我说“圆形包装”时，我的意思是(引用维基百科):

> 给定曲面上的圆(大小相等或不同)的排列，使得不发生重叠，并且所有的圆都互相接触

想想[这样的事情](https://raw.githubusercontent.com/buntine/pioneers/master/data/screenshots/impact.png)(剧透预警！)或者[这个](http://www.adammarcus.com/wp/wp-content/uploads/2014/01/circle-packing-574x347.jpg)。事实证明，这远比我最初想象的更有趣(也更具挑战性)。我很快决定不使用现成的数据 viz 库，因为我想对最终效果进行深度控制，不想给用户增加不必要的依赖，而且，见鬼，我想学习一些有趣的东西！

事实证明，某些形式的循环包装被认为是 NP 难的。但是考虑到我们正在处理一个可视化，我们并不试图找到可能的“最好的”包，而只是一个对人类观察者来说视觉上“足够好”的包。我们真幸运！我最终想出的算法是，通过对每个圆施加吸引力，将它们拉向画布的中心点，来执行连续的细化，当两个圆碰撞时，这种吸引力会稍微超过分散力。我们执行的精确优化次数是我们正在打包的圆数的函数。为了在每个圆之间添加像素填充，我们简单地假设每个圆的半径比实际大。这种方法的一个好处是它使得动画制作变得微不足道；这只是在算法的每次迭代中重新定位每个圆的问题。

至于工具，我决定保持一切简单。网站是用[字体](https://www.typescriptlang.org/)和 Python 实现的。我用一个简单的调度模式代替了一个潜在的免费前端框架(我确信四人帮给它取了一个好听的名字，我已经忘了)。状态由一个简单的三项字典处理:`tab`、`tags`和`operation`。我确实利用了(非常方便的) [Snap SVG](http://snapsvg.io/) 库，以便在运行时包装 SVG 元素的物理生成。我将把我对 TypeScript 作为一种可行语言的分析留到另一篇文章中。

在这个项目中，我意识到数据的收集和管理需要付出多少努力。我一定花了至少 50 个小时在网上搜索出生日期、合适的照片和成就的细节。然后，当然，我努力尝试对我根本无法真正欣赏的成就进行“排序”。例如，1971 年，斯蒂芬·库克发表了一篇里程碑式的论文，提出了现在广为人知的 [P vs. NP](https://en.wikipedia.org/wiki/P_versus_NP_problem) 问题。这是*至今*计算机科学中一个未解决的问题。我知道这个问题说明了什么，我也知道它非常重要。但是，由于最近没有接受理论计算机科学的教育，我很难自信地宣称*对这个领域有多么重要。希望随着时间的推移，其他人会帮助我纠正我在这方面的一些错误。*

由于整理这些数据花费了我大量的时间，我觉得以一种可解析的格式向社区展示这些数据是很重要的。因此，我将当前的数据集打包成 CSV 格式，并将其存放在官方存储库上。

无论如何...事不宜迟，我介绍:[计算机科学的先驱](http://computerpionee.rs/)