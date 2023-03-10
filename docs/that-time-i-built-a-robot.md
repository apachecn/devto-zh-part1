# 那次我造了一个机器人。

> 原文：<https://dev.to/kaydacode/that-time-i-built-a-robot>

我一直对机器人很好奇。作为一名移动开发者，我意识到技术变化的速度有多快&也明白我可能不会在 10 年后开发 iPhones。我可能在做某种软件-硬件-Alexa-人体组合。谁知道呢。

机器人。车轮在大学开始回转。我有一个为机器人编写程序的教授，我被记住了。他的机器人与其说是出于实用性，不如说是出于幽默，但它们的基础仍然令人印象深刻。一个小机器人快速而偶然地来回走动，唯一的目的是娱乐猫，或者一台电脑被侵入一个动力轮，在校园人行道上行驶。我们看着网络摄像头的实时流，而它巡航左右..至少可以说很有趣。

在某个时候，我看到了这本书:[机器人:基于项目的方法](https://www.amazon.com/Robotics-Project-Based-Approach-Lakshmi-Prayaga-ebook/dp/B00PG922M4)。我遇到的一个缺陷是它不是 100%完整。零件不见了，说明书不见了，还有些猜测。希望能在这里和我的[个人网站](https://kimarnett.com/blog/)上写博客，因为我在努力完成这本书来填补一些空白。尽管如此，我还是推荐它。有很多很棒的代码样本。我也将张贴我如何建立这个，所以你也可以建立你自己的。

## TLDR:

## 我造了什么(&很快你也可以了):

项目 1 是一个原型 Roomba。Proto-roomba 漫无目的地绕着房间漫游，避开墙壁。仅此而已。

[![](img/1c85d5ed503ab2bc48523b106a4b61c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zTZnPEjP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i1.wp.com/www.kimarnett.com/blog/wp-content/uploads/2017/02/C5n1HOJWQAIsllH.jpg)

**破落**
机器人的大脑全部通过 Arduino Uno。Uno 是一台计算机，负责协调哪些端口何时通电。(我会在下一篇文章中更好地解释这一点)Uno 夹在机器人的后半部分，在电池组(底部)和 Ardumoto 盾(顶部)之间。

Ardumoto 屏蔽将通过可堆叠的接头插入 Uno，您需要焊接到位。(项目 1 仅要求焊接)。Ardumoto 是控制底盘马达的部件..开、关、顺时针或逆时针。

另一个重要的部分是前面的蓝黑眼睛。这是超声波传感器，当墙壁或物体靠近时，它会告诉 Uno。基于这个值，你的代码将告诉电机转动。

## 物资:

这是你开始学习机器人所需要的用品。

1.  Arduino Uno R3 - [新蛋 4.09 美元](https://www.newegg.com/Product/Product.aspx?Item=9SIA7BF2K19064&nm_mc=TEMC-RMA-Approvel&cm_mmc=TEMC-RMA-Approvel-_-Content-_-text-_-)

2.  Ardumoto Board - [亚马逊 6.99 美元](https://www.amazon.com/gp/product/B01K1XTIUI/ref=oh_aui_detailpage_o03_s00?ie=UTF8&psc=1)

3.  可堆叠插头(推荐至少 2 个)- [Sparkfun $1.50](https://www.sparkfun.com/products/10007)

4.  魔术师机箱- [亚马逊 29.95 美元](https://www.amazon.com/gp/product/B007R9U5CU/ref=oh_aui_detailpage_o07_s00?ie=UTF8&psc=1)

5.  试验板(需要 1 个)- [亚马逊 9.00 美元 3 包](https://www.amazon.com/gp/product/B01258UZMC/ref=oh_aui_detailpage_o04_s01?ie=UTF8&psc=1)

6.  超声波传感器(需要 1 个)- [亚马逊 2 包 6.38 美元](https://www.amazon.com/Ultrasonic-Distance-Measuring-Compatible-Duemilanove/dp/B01M13S26V/ref=sr_1_4?ie=UTF8&qid=1493919724&sr=8-4&keywords=Arduino+Ultrasonic+Sensor)或者[spark fun 3.95 美元](https://www.sparkfun.com/products/13959)

7.  无焊跳线- [亚马逊 7.20 美元](https://www.amazon.com/gp/product/B00W8YFSPI/ref=oh_aui_detailpage_o07_s00?ie=UTF8&psc=1)

8.  烙铁/焊料**我从当地的电脑商店买了一套“学习焊接工具包”。我强烈建议在你尝试焊接 Uno 之前进行类似的练习(如果你是新手的话)。

9.  电脑螺丝刀套装- [亚马逊 13.29 美元](https://www.amazon.com/gp/product/B01EFMLOLM/ref=oh_aui_detailpage_o07_s00?ie=UTF8&psc=1)

## 立即吸取教训:

*   **调试硬件和调试软件是完全对立的。你将同时做这两件事。**

*   **焊接后&前测试硬件。**
    我很幸运没有遇到任何问题，但是我看到其他人发现他们的主板不工作了。如果你在焊接前发现这一点，很容易联系公司并更换坏零件。如果你在焊接后发现这个，很可能是焊接问题&公司不会更换它。(我确实不得不购买另一个 ardumoto 板，因为我焊接了错误的部分。所以每样东西都检查两遍，但是如果你买了我上面建议的东西，应该就不错了)。

*   在小的迭代中频繁地编码/构建/测试。
    可能是连接不好。
    可能是电量较低。
    这可能是你的代码。
    它可以是任何事物的组合。你会感到沮丧，所以保持小的迭代有助于排除过程。让你 10 分钟前测试过的东西工作比让你从昨天开始就没有测试过的东西工作要容易得多。

*   意识到你所学的一切都可以应用到其他项目中。
    如果你不喜欢我的 proto-roomba，还有一千个其他漂亮的机器人。Spark fun 有 100 万个组件，你可以尽情享受。玩得开心。:)

敬请关注 Proto-roomba 的完整教程！同时，请随时在下面留下任何想法或问题。