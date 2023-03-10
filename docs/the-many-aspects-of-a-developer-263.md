# 开发人员的许多方面

> 原文：<https://dev.to/pbeekums/the-many-aspects-of-a-developer-263>

我努力让明天比今天更好。我一直在思考我的强项和弱项。然而，不可能擅长所有的事情。这意味着我需要有选择地花时间去提高。什么能让我的时间最有价值？

[![](img/f84fcea97faa8f2103ce5b690c00aad7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--U6mMPoaE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.professorbeekums.com/img/2017/9/aspects.png)

这个思考过程不仅仅是为了个人成长。在招聘开发人员时，知道有哪些不同的技能组合，并能够在其他技能组合中识别它们是至关重要的。我和我的一个团队犯了一个错误，雇佣了一个优秀的开发人员……他擅长我们没有做的事情。不是所有的开发者都擅长同样的事情。不是所有的团队都需要他们的开发人员擅长同样的事情。

最明显的开发人员技能是编程能力。开发人员的工作是写代码，所以一个优秀的开发人员应该写出优秀的代码。不幸的是，事情没那么简单。能够确定编程能力有很多细微差别。

编程实际上只是一种与计算机交流的方式。开发者用**编程语言**编写代码的事实强调了这一点。打个语言的比方:懂英语是一回事。能不能用英语写诗是另一回事。或者虚构小说。或者是面向大众的非虚构作品。或者科学论文。懂一门语言是一回事。能不能用好，取决于一个人在一定语境下对该语言的专业知识。

继续类比英语写作，作者和编辑的技能是不同的。在需要理解好的语法、句子结构等方面有很多重叠。但是心态不同。对于开发人员来说，从零开始编写代码和在现有系统上迭代有着巨大的区别。除非一个开发人员是一个项目的创始人，否则他很可能具有在现有系统上迭代的专业知识。

我早期职业生涯的大部分时间都是这样。大学毕业后的头几年，我很少从头开始创建项目。我的工作已经有了现有的产品。这是做出决策的基础。一个现有的系统已经具有工作功能，不破坏该功能的努力对可以做的事情造成了限制。我的技能是评估这些限制是什么，以及在这些限制内我能做什么。一个编辑不能决定将一部谋杀悬疑小说改编成一部爱情小说。这是作者已经决定的事情。

再恐怖的底子也还是底子。它仍然减少了开发人员必须选择的选项数量。这使得决定做什么更容易。对于一个糟糕的基金会来说，这条路可能是不愉快的，但做出选择这条路的决定是相对简单的。

从零开始创建一个项目意味着无限的可能性。没有什么是你不能选择去做的。开发人员可以选择回到他们以前的做法，但通常这并不是最好的方式。他们以前做事的方式是基于他们当时的限制。没有了这些限制，他们现在有了更好的选择。诀窍在于能够在无限多的可能性中做出一个好的决定。这是一种与使用现有软件系统完全不同的技能。

还有调试代码的能力。你可能认为这和写作是并行不悖的，但是思维过程是完全不同的。写代码的时候，开发者就是驱动者。他们知道他们需要建造什么，也知道如何建造。他们正在做各种选择。x 要建在前端。y 建立在后端。z 进入弹性搜索。

调试的时候，事情就不那么清楚了。用户不知道一个系统是如何构建的，也不关心。他们期望事情能够成功。当某样东西不起作用时，他们说它不起作用。然而，在代码中找到问题发生的确切位置可能需要几分钟到几周的时间。

假设一个网站的搜索功能坏了。用户输入一些东西，但什么也没发生。用户所知道的是，当他们点击搜索时，什么也没有发生。这是开发者开始调查的地方。

*   浏览器控制台中是否有错误？
*   如果是，是因为服务器返回了错误的数据吗？
*   如果服务器返回坏数据，是因为服务器代码坏了还是前端代码没有发送正确的参数？
*   如果开发人员可以将问题缩小到服务器上，那么他们必须开始调查服务器上的什么地方。
*   是密码吗？
*   是数据存储吗？
*   代码和数据存储之间是网络吗？
*   如果在代码中，是与开发人员编写的代码在一起，还是与开发人员使用的第三方库中的代码在一起？

如果开发者真的能重现这个问题，这些只是问题。有时问题可能与位置有关。开发人员通常使用 cdn 向浏览器交付代码和图像。cdn 将这些资产的副本存储在世界各地。这使得网页加载速度更快，因为亚洲的某些人不必等待从美国传来的图像。但是这也打开了 CDN 在亚洲失败的可能性，所以只有亚洲的人才会发现问题。美国的开发者会感到沮丧，因为他们看不到用户抱怨的问题。就像你觉得自己没味道却听到有人抱怨你的味道。令人沮丧。

还有其他类似的令人沮丧的例子，但关键是调试和写代码是不同的技能。与其说是在建造什么东西，不如说是在做调查/侦探工作。所需的创造力类型将会不同。

回到写代码本身，能写出快速的代码和能写出其他开发者能读懂的代码是两码事。人们需要更强的数学能力。另一个需要组织能力。虽然只与在这两方面都很出色的开发人员一起工作会很好，但现实并不总是美好的。

不同的软件将受益于倾向于这种或那种方式的开发人员。不过，软件的好处在于，你可以通过拥有多名拥有各种技能的开发人员，获得拥有一名“超级棒的神级开发人员”的大部分好处。一个具有优秀组织技能的开发人员可以为整个系统奠定基础，而编写真正快速代码的开发人员可以专注于插入的算法。

[![](img/1befbf2288b0743883637ae24b5801c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--0GcD-e7B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.professorbeekums.com/img/2017/9/wealth_of_nations.jpg)

虽然每个开发人员都擅长所有事情是最理想的，但是时间是有限的资源。开发者将倾向于某些方向。这也是一件好事。在软件领域，你只需要一个专家就能提高整个团队在该领域的能力。对于开发人员来说，这意味着不一定要做他们不擅长的事情。专注于继续发展自己的优势是有意义的。

对于招聘经理和面试官来说，这也意味着在招聘开发人员时没有一个模板。他们应该考虑现有团队目前的优势和劣势，并寻找能够填补这些缺口的人。