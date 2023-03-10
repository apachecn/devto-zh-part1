# 100%代码覆盖率的悲剧

> 原文：<https://dev.to/danlebrero/the-tragedy-of-100-code-coverage>

*本文原载于 [IG 的博客](http://labs.ig.com/code-coverage-100-percent-tragedy)*

事情的变化很有趣。十五年来，我一直在鼓吹 TDD，或者至少让开发人员编写一些单元测试。然而，最近我发现自己更经常地说，“你为什么要写那个测试？”而不是“你应该写一个测试。”

## 到底是怎么回事？

当我在办公室走来走去的时候，一个开发人员让我帮他做一些单元测试。似乎他在使用 Mockito 测试下面这段代码时遇到了麻烦:

[![mockito](img/823e33c47b0ca2d6fe72951bfcb6aaba.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aB38Ro3H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://labs.ig.com/sites/default/files/init_0.png)

我想他对我的回答感到非常惊讶:“你不需要测试那个。”

“但我不得不！”他说。“那我怎么知道代码是否有效呢？!"

“代码很明显。没有条件，没有循环，没有转换，什么都没有。代码只是一点普通的旧胶水代码。”

"但是，没有测试，任何人都可以来，作出改变，打破代码！"

“看，如果那个虚构的邪恶/愚蠢的开发人员来破坏了这个简单的代码，如果一个相关的单元测试失败了，你认为他会做什么？他只会删除它。”

"但是如果你不得不写测试呢？"

在这种情况下，我会这样测试它

[![no-mockito](img/caf6f9da14ead92b15e44a1a3b7fc9c5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1TYJX-J0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://labs.ig.com/sites/default/files/init-test.png)

“但你不是在用莫奇托！”

“那又怎么样？莫奇托帮不了你。恰恰相反:它妨碍了你，不会让测试更具可读性或更简单。”

"但是我们决定在所有的测试中使用 Mockito！"

我:“…”

下一次我碰到他时，他自豪地说他已经设法和莫奇托一起写了测试。我理解让它工作的精神满足感，但尽管如此，这让我很难过。

## 再比如

我被一个开发者拉了进来，他对他们的一个新应用程序的高代码覆盖率和他们对 BDD 的新爱好感到兴奋。环顾代码，我们发现下面的[黄瓜](https://cucumber.io)测试:

[![cucumber](img/11dbd57081046b91cd316d87fb2da4d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rYMomaNQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://labs.ig.com/sites/default/files/cucumber-test.png)

如果您以前使用过 Cucumber，您不会对它所需的支持代码数量感到惊讶:

[![support cucumber](img/5200422334a68e25933076f99626b428.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tN2NwzUw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://labs.ig.com/sites/default/files/cucumber-support.png)
[![support cucumber 2](img/b3b56ab44e05d5038805b0dd895bee8d.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--DPiGJb1U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://labs.ig.com/sites/default/files/cucumber-support2.png)

所有这些都需要测试:

[![cucumber sut](img/1c7fa9c479b5d6b69e84252998444fe3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CsbRywih--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://labs.ig.com/sites/default/files/cucumber-code.png)

是的，简单的地图查找。

我对开发人员有足够的信任，可以直截了当地说，“这是对时间的极大浪费。”

“但是我的老板希望我为所有的课程编写测试，”他回答道。

“以什么为代价？”

“费用？”

"无论如何，那些测试与 BDD 无关."

“我知道，但是我们决定所有的测试都用黄瓜”

我:“…”

我理解让工具屈从于你的意志所带来的精神满足，但尽管如此，这还是让我很难过。

## t 肆虐在哪里？

悲剧在于，两个聪明的开发人员(我会带他们去参加一个团队面试)正在浪费时间编写那些毫无意义的测试，这些测试需要由未来一代的 IG 开发人员来维护。

悲剧在于，我们没有使用正确的工具，而是决定继续使用错误的工具，没有特别好的理由。

悲剧在于，一旦“好的实践”成为主流，我们似乎忘记了它是如何产生的，它的好处是什么，最重要的是，使用它的成本是多少。

相反，我们只是机械地应用它，而没有过多的思考，这通常意味着我们最终充其量只能得到平庸的结果，失去了大部分收益，但付出了所有(甚至更多)的成本。根据我的经验，编写好的单元测试是一项艰苦的工作。

## 那么 100%的代码覆盖率值得追求吗？

是的，每个人都应该在一个项目中实现它。我认为你必须走极端才能知道极限是什么。

我们已经有很多关于一个极端的经验:没有单元测试的项目，所以我们知道在这些项目上工作的痛苦。我们通常缺乏的是在另一个极端的经验:项目中 100%的代码覆盖率被强制执行，一切都是 TDD。

单元测试(尤其是测试优先的方法)是一个非常好的实践，但是我们应该知道哪些测试是有用的，哪些是适得其反的。

但请记住，没有什么是免费的，没有什么是银弹。停下来想一想。