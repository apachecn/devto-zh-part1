# 陷入困境时要问自己的 4 个问题

> 原文：<https://dev.to/jakoblind/4-questions-to-ask-yourself-when-you-get-stuck>

嗨，我写这篇文章时考虑的是 React 开发人员，但它适用于所有类型的语言和库！尽情享受吧！

* * *

看看这些常见的问题和答案:

我应该把我的状态放在哪里？
A: *将它放入 React 组件的 2 个选项。或者用 Redux。或者 Mobx。或者……*

问:我应该把我的逻辑放在哪里？
答:*容器组件或动作创建者或实用程序库或…*

问:我应该如何做测试？
答:*你有 5 个断言选项，4 个存根选项，3 个间谍选项，4 个测试运行程序选项……*

看到图案了吗？对于每个问题，你都有一个解决问题的习惯方法列表。

你应该选择哪种解决方案？理想情况下，你只想要一种惯用的方法来解决你的问题，但是你得到了一长串的选项，却不知道何时选择哪一个。

所以你会感到困惑。然后你就卡住了。

有时甚至没有一个惯用的方法来解决你所遇到的问题。尽管它们是常见的问题。

“我应该使用什么文件结构？做你想做的事。
“我需要的最少一套工具是什么？*视情况而定……*

那也没用…

我们想要的只是一个清晰简单的照章办事的解决方案。但是在 React 生态系统中没有，那么我们应该做什么呢？

放弃？

# 你问自己的问题很重要

所以当你遇到问题时，你会问自己这个问题:“解决这个问题的最正确/惯用的方法是什么？”。

这个问题可以多次帮助你解决你的问题，或者引导你走向正确的方向。但这也可能导致更多的困惑，而不是清晰。

判断一个人，要看他的问题，而不是他的回答–伏尔泰

现实世界是复杂的，找到合适的解决方案需要你从多个角度来看待问题。

你这样做的方式是问许多种问题。以下是一些例子:

## 1。“我能做的最简单的事情是什么？”

有时有必要用 Thunk 中间件创建 Redux 存储和动作来处理 Ajax 请求。

有时一个 4 行的 HTML 表单标签就足够了。

不要总是去寻找“正确而花哨的解决方案”,而是停下来思考一下，你能实现的解决问题的最简单的方法是什么。一个简单的解决方案更容易维护，错误风险更小，通常也是最优雅的。也是最快实现的。

## 2。“我在试图解决什么样的问题？”

*   性能问题？
*   上市时间至关重要的特性？
*   这是概念证明吗？
*   内部工具？
*   为了学习？

如果学习是目标，您可能想要编写相同特性的两个或三个不同的实现，只是为了探索和比较。如果上市时间很重要，您可能会选择最快的实现方式，即使它是“错误的”。

## 3。“我的队友能力水平如何？”

很多大三？仅限老年人？如果你将招募更多的开发人员，他们的个人资料是什么？这些是需要考虑的真正重要的问题，因为它说明了“你允许代码库拥有的聪明程度和创造性空间”。

## 4。"我能现在做决定，以后再考虑吗？"

有些情况下，最好的办法是制定一个足够好的解决方案，并在以后重新审视它。如果你认为在第一次尝试时就能使它变得完美，那么你就有过度设计的风险，结果会使代码变得更加复杂。

你还不知道代码库将如何发展。也许这段代码不会像你想象的那么长。也许一个月后这个特性会从产品中完全删除。(在我身上发生过很多次)

*慎用此问题！*你不希望到处都是快速解决方案。有一个平衡。

# 结论

每个应用程序都是独特的，寻求通用的解决方案并不总是最好的方法。要找到应用程序中问题的最佳解决方案，除了官方文档和教程之外，您还需要进一步思考。

现在你有了一个工具，它考虑了许多变量来寻找专门的解决方案，这将导致更可读和更可维护的代码。

希望最终有更多快乐的最终用户！