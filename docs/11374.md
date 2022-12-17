# 生活的游戏

> 原文：<https://dev.to/funkysi1701/game-of-life-4dhp>

很多很多年前，我试图学习 Java。我没走多远，但我建立的东西之一是生活的游戏。

不幸的是，我找不到我写的代码，可能是一件好事，因为它可能是可笑的坏。

现在你大概在问什么是人生的[游戏？生命的游戏，有时被称为细胞自动化，是对细胞出生和死亡的模拟。它有时被称为康威的生命游戏，以 1970 年发明它的英国数学家命名。](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life)

[![](img/7b62613eb6c1726d6c140bb824ad4fc5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--mm4G4y35--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://upload.wikimedia.org/wikipedia/commons/e/e5/Gospers_glider_gun.gif)

这个游戏有几个规则:

1.  任何少于三个邻居的细胞将在下一轮死亡
2.  任何有三个邻居的细胞都可以存活到下一轮
3.  任何有三个以上邻居的细胞将在下一轮死亡
4.  任何有三个活邻居的死细胞将在下一轮出生

直到前几天我偶然发现了一个[博客](https://chaosnil.wordpress.com/2015/02/09/new-project-conways-game-of-life-in-every-language/)和一个 [github 页面](https://github.com/chaomodus/gameoflife)，这才再次引发了我的兴趣。这个版本是用 javascript 编写的，但是博客提到了用多种不同语言构建这个程序的愿望。我想用 C#的 visual studio 来构建这个，因为它应该提供一个很好的训练练习。

有几个问题我需要克服，以实现这一点，我目前正试图找出如何“画”的细胞。我目前的想法是，也许我可以用字符来显示单元格，然后在逻辑正确时用像素来替换。我要学的还有很多！