# 为什么你应该把用户界面留到最后

> 原文：<https://dev.to/loicniragire/why-you-should-save-the-ui-for-last>

*在我们的行业中，一个非常常见的做法是首先构建 UI，然后在后端进行绑定*。在很大程度上，争论的焦点是最终用户从一开始就更多地参与到这种方法中，因此这给了我们信心，我们确实在构建正确的产品。**我已经意识到这种方法的一个主要缺点是，我们最终往往会得到一个糟糕的领域层或者缺乏领域层**。因为我们如此专注于将数据输入到我们闪亮的 UI 中。这里有一个简单的例子来说明我的观点。将 UI 和数据层从应用程序中分离出来，看看剩下的部分能推理出多少。换句话说，当所有的花里胡哨都被剥掉后，你还剩下什么？你的领域有多丰富，或者你的领域是什么？**你的应用程序是关于什么的，在你的代码中有多明显？**

好的，也许你可以提出这样一个观点，有一个两层的应用程序，并不是所有的东西都需要领域驱动。明白了。我所看到的朝着丰富领域发展的最令人信服的原因是，对你的应用程序进行测试和推理很容易。我发现将我的应用程序视为服务用例而不是服务数据非常有帮助。巨大的差异。还有什么地方比丰富的领域更能锻炼你的用例呢？

**那么什么是富域呢？我认为它是一个表达我的业务领域和允许的用例的层。它有多丰富，取决于它有多精通。因此，下一次你发现自己在表达要服务的用例之前设计用户界面时，我的建议是从后者开始，一步步往上走。**让你的 UI 插入你的域。****