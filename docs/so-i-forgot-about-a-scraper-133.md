# 所以我忘记了刮刀

> 原文：<https://dev.to/engineercoding/so-i-forgot-about-a-scraper-133>

早在 4 月份，我就联系了 dev .以确定是否有可用的 API。我需要那个 API，因为当时(现在也是！)我以为内容值得保存作为参考。我编写了一个简单的 python scraper，并将纯文本转换成 PDF，因为这些对我来说可读性更好。我开始在我的树莓派上运行这个程序。

我的数据存储越来越多，但我从来没有读过任何东西。这是毫无意义的，并决定拔掉它的插头。至少，我以为我拔掉了插头。我记得禁用了 cronjob，但今天我又用 cronjobs 做了些手脚，发现它一直在运行。我觉得这个刮刀已经运行了至少 7 个月了。

编辑注:没有拔掉插头的原因是因为我的 SD 卡处于只读模式。这意味着我可以做出改变，而这些改变只能存在于内存中。当 RPI 重新启动时，数据消失了。这使得这些数据变得毫无用处，因为这些数据是在 pi 运行的某个时间点+天数内收集的。

请注意，数据只是文章的原始文本，而不是评论等。讨论话题在四月并不算什么。

*免责声明:这些数字可能不是 100%准确，因为它只是一个快速脚本*

首先，我决定获取数据集的一些基本统计数据:

| 种类 | 统计的 |
| --- | --- |
| 文章数量 | One thousand seven hundred and fifty-nine |
| 总尺寸 | 196608 字节 |
| 作者数量 | Seven hundred and ninety-three |

然后我想知道谁是前 10 名的海报:

| 谁 | 员额数量 |
| --- | --- |
| 本·哈尔彭 | One hundred and twenty-eight |
| 瓦伊代希·乔希 | Thirty |
| 张碧琪 | Twenty-five |
| 沃克·哈里森 | Twenty-four |
| 电子设计自动化 | Twenty-two |
| K.👓 | Twenty-one |
| 肯·W·阿尔杰 | Nineteen |
| 菲尔·纳什 | Eighteen |
| 瑞安·帕洛 | Sixteen |
| 开发到员工 | Sixteen |

为了好玩，我检查了一些术语，看它们在独特的文章标题中有多少。注意，对于像“go”这样的词，考虑到它可能有其他含义，因此这些是精心挑选的。

[![A graph with random terms](img/30be4749db4bfa834bc3c661518f372f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pZG7dXfp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uo0fd4gb88564ozzyeaa.png)

现在我会关掉我的刮刀，但我会保留这个语料库来练习一些文本挖掘。如果你有关于如何处理这些数据的想法，请分享！