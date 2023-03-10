# 对代码覆盖率持保留态度的五个理由

> 原文：<https://dev.to/scottshipp/five-reasons-to-take-code-coverage-with-a-grain-of-salt-2j0>

代码覆盖率很重要。我鼓励任何人都这样做，我认为这对任何软件团队来说都是一个简单而有价值的实践。我也不是那种认为单元测试毫无意义的人，也不是那种当他们说单元测试时就意味着集成测试的人。

作为一名工作软件工程师，我喜欢高代码覆盖率。在一个代码覆盖率高的代码库中，我的工作要愉快得多，因为我在实现过程中不得不做的事情数量大大减少了。

但是关于代码覆盖率的事情是这样的:我真的必须确保对此有所保留。看到这么高的数字就像喝了几杯威士忌。压抑太容易失去了。纪律有点过时了。因为，嘿，测试会抓住我的，对吧？

不对！这里有五个原因可以解释为什么代码覆盖率会误导我们中最优秀的人。

**1。所使用的代码覆盖率类型对覆盖率数字的含义有很大的影响。**
我不知道你的情况，但是在我工作过的任何地方，获得适当的代码覆盖率都是一件苦差事，因为我几乎总是从某人或某个团队那里继承一个项目，几乎没有留下任何单元测试。在这种情况下，团队通常同意从语句(或行)覆盖率开始度量代码覆盖率。

然而，语句覆盖率的价值很低。有些人可能会认为这根本不是一个好的衡量标准，我可能会同意，这取决于具体的日子。分支和路径覆盖都更好地揭示了关键客户场景未被测试的地方。

简而言之，代码覆盖率应该是实际用户是否拥有他们所需要的功能的一般指示。通常情况下，高语句覆盖率与高用户价值并不匹配。我建议使用分支覆盖。

**2。被测试的应用程序的类型确实会改变数字的含义。**
软件团队的目标应该是多少百分比？75%?80%?95%?虽然这本身总是一个很难回答的问题，但我知道它实际上应该根据应用程序而变化。一些应用程序纯粹是协作服务之间的桥梁。其他的与硬件紧密相关，甚至嵌入在硬件中。还有一些更多的是关于数据而不是行为。

例如，有些应用程序实际上只是一堆数据类和一个数据存储接口。大部分代码是我称之为 POJO 的(因为我目前是一名 Java 开发人员)，或者在其他语言中可能被称为 POCO 的或 POPO 的或 POO 的。

是否应该覆盖数据类以满足目标覆盖率？小心行事！可用的选项是用低价值的测试覆盖它们，然后必须维护它们，从代码覆盖中完全排除它们，或者保留它们而不排除它们。我通常尝试以这样一种方式来组织事情，我可以将一些包排除添加到我的覆盖率测量工具中，并将这些排除在外。但是无论哪种方式，无论我选择哪个选项，我的代码覆盖率都不能说明全部情况。

**3。测试可以执行代码行，但最终什么也验证不了。**
很多时候，程序员通过阅读文档中给出的例子来学习编写单元测试，不管他们使用的是什么测试或模拟框架。许多文档都正确地围绕如何使用框架特性，而没有教导如何测试。这为实际测试中常见的失败敞开了大门。

以流行的 JS 测试框架 Jasmine 的[文档为例。指南的大部分给出了重复的例子，测试只证明了测试代码本身写了什么。这有利于让您快速理解 Jasmine 的可用特性，但对于更多的新手开发者来说却是不好的，他们可能会错误地认为这是一种测试方式。很快，他们就开始编写无法验证任何东西的测试。](https://jasmine.github.io/2.0/introduction)

即使是有经验的开发人员也会偶尔失误，写出无法验证任何东西的测试。而一个代码覆盖率数字并不能揭示这一点！因此，除非我实际上保持自律，并花时间来验证我的测试执行了代码*并且*验证了结果，否则我的测试具有高覆盖率但价值低。除了测量代码覆盖率之外，软件团队还应该进行仔细的代码评审，从而产生高质量的测试。

**4。代码覆盖率并不能表明应用程序设计得有多好。**
我能在设计糟糕的应用程序中获得良好的代码覆盖率吗？绝对的！类可以设计得很差，但仍然是可测试的。使代码可测试通常会改进它的设计，但是也有很多陷阱。例如，使类可测试的一种方法是将类成员公开，以便有办法设置测试双精度。嗯...不是最好的主意。

不幸的是，我认为有一个神话流传开来，那就是可测试的代码是设计良好的代码。我的猜测是，这是一个[肯定随之而来的谬误](https://en.wikipedia.org/wiki/Affirming_the_consequent)。多年来，许多作者和演讲者都正确地指出，当代码设计良好时，它是可测试的。在日常实践中，我认为开发人员将这两者互换，并开始认为如果代码是可测试的，那么它必须是设计良好的。当心谬误！

**5。代码覆盖率并不意味着测试是可维护的。**
我不知道其他人看到了什么，但是我看到了一些非常好的代码库由于不可维护的测试而慢慢失去了代码覆盖率。关于测试代码的可悲事实是，它经常得到二等公民的地位，程序员往往对那里发生的事情缺乏纪律性。很快，测试变得不可读、易变、难以扩展，甚至更糟。如果没有让他们回到良好状态的纪律，死亡螺旋很快就会开始。

重要的是要记住，测试是有帮助的，但是只有当它们最终有助于团队的速度。一个耗费团队时间和精力的测试套件可能会被标记为“忽略”所以即使代码覆盖率很高，也要注意保持纪律，因为雪崩可能就要来了。

**临别赠言**
考验是很多东西，但*容易*不在其列。继续测量代码覆盖率。继续努力，争取一个目标覆盖率。继续吧，甚至对要求代码覆盖率高于或达到目标的拉取请求设置一个门。但要小心这可能带来的过度自信的感觉。代码覆盖率并不是一切，仍然会有很多地方出错。良好的纪律和良好的实践，如代码审查，仍然比代码覆盖率更有价值。