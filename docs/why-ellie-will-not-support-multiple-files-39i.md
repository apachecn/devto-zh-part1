# 为什么 Ellie 不支持多个文件

> 原文：<https://dev.to/lukewestby/why-ellie-will-not-support-multiple-files-39i>

Elm Live 编辑器 Ellie 让你在浏览器中使用 Elm 平台。我在 2017 年 2 月发布了它，作为一种让 Elm 社区更容易共享代码的方式。尽管它在仍然很小且不断增长的 Elm 社区中很受欢迎，但它仍然是一个副业项目，所以我需要明智地决定我如何花时间在它上面。它的受欢迎程度也预示着它对社区的重要性，我相信这伴随着以满足社区需求的方式发展它的责任。

Ellie 不仅仅是一个共享 Elm 代码的工具；它正成为在线互动讨论 Elm 的核心。每当有人在 [Elm Slack](http://elmlang.herokuapp.com) 中分享 Ellie 链接时，我都会收到推送通知。一个典型的工作日包括至少一次对话，参与者来回分享 Ellie 链接，以帮助某人更好地理解 Elm 概念。[在今年的奥斯陆榆树日](https://www.youtube.com/watch?v=GwmVELtQnOI&t=1689s)，我声称为了写出好的软件，我们需要了解人们如何使用它。几个月后[在 Elm Conf US](https://www.youtube.com/watch?v=gNWx-zWxUd4) 上，我特别分享了关于 Ellie 的结论:Ellie 使关于 Elm 的对话成为可能。

这个发现的身份指导着埃莉的使命，并将告知我所有关于功能和 UX 前进的决定。这导致了实时协作的计划增加。它还可能导致更多个性化 Ellie UI 的方式，包括添加更多熟悉的编辑器键映射，如 Emacs 和浅色主题。然而，除了积极的添加之外，即使面对用户的请求，它也为不包含某些特性的决策提供了信息。

当然，我说的是在一个 Ellie 项目中包含多个文件。这是最受欢迎的功能之一。但是，就像 Elm 本身一样，Ellie 的发展并不是由直接民主推动的。我不认为允许包含多个文件符合在线讨论 Elm 的标准。

原因如下:如果你在一个宽泛的话题下问我一个关于具体概念的问题，如果我发给你一个 4000 字的中型帖子的链接，你可能不会欣赏。那会结束谈话！我想简短地回答，这样你就可以考虑我说的话，并在此基础上继续前进。对于理解 Ellie 的主要用例，聊天应用程序是一个更好的类比。在这两种情况下，你可以想打多少就打多少，但媒体鼓励你简短，因为你是在对话。

我听过一个对位法，有些概念甚至不能在 Ellie 中简单解释，因为它们依赖于拥有多个文件。例子包括 Elm 模块系统本身和不透明类型。我对此的回应是，这一类别中的概念数量非常少，我宁愿 Ellie 在这一小组案例中不可用，也不愿将 Ellie 的重点分散在简洁的对话和大型项目之间，从而使两者都变得更糟。

也许其他人会开发软件，允许人们在线构建和运行大型 Elm 示例。甚至有一天可能是和 Ellie 有关的项目，就像 CodePen 推出 CodePen 项目一样。然而，Ellie 的核心焦点是对话和优化，对我来说，这比让它满足所有代码共享需求更重要。