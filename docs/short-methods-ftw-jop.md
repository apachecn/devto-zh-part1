# 短方法 FTW

> 原文：<https://dev.to/vladimirwrites/short-methods-ftw-jop>

[![Header](img/52be19a1db10723d472353f6508792a5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LFR8MDd8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AlEFA9LXXUbGBMnJcEw6fAg.jpeg)

大约 5 年前，我在一家小公司找到了第一份实习工作。我们在 Matlab 中构建了一个计算机视觉原型，我的任务是编写 C/C++代码，用来对视频帧进行不同类型的转换。我写了一个 300 行长的方法怪物，在矩阵上做各种变换和计算。这很有效，我很开心。然后我的老板找到我说:

> 你写的每个方法应该少于 25 行。

我笑了，笑得很厉害，对着他的脸。我真是个笨蛋！

[![Plonker](img/3f6a3da3680f4f92885b0cbfe51a68e2.png)T2】](https://i.giphy.com/media/zI6X93e09lDag/giphy.gif)

让我告诉你，25 行组成一个又大又长的方法。实际上，大多数应该更短，大约 10 行，甚至更少。25 行应该是上限了。这是为什么呢？！以下是一些原因:

*   首先，它很容易放在你的屏幕上，所以很容易阅读。
*   它的目的可以用一句简单的话来表达，这使得它(在大多数情况下)容易理解。
*   它不需要注释，因为[它只做一件事](https://en.wikipedia.org/wiki/Single_responsibility_principle)，那件事可以在它的名字内描述。
*   很容易测试。
*   最后，因为鲍勃叔叔说的。

> “函数的第一条规则是它们应该很小。函数的第二个规则是它们应该比那个小。”罗伯特·马丁

但是，并不是所有的短方法都是好的。比如这个: