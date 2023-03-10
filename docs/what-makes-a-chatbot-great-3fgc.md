# 聊天机器人的伟大之处是什么？

> 原文：<https://dev.to/suhas_chatekar/what-makes-a-chatbot-great-3fgc>

这篇文章最初发表在我的博客上。

上个月，我参加了大数据伦敦会议。微软运行的[会话中有一张幻灯片(#7)，大致内容如下](https://www.slideshare.net/MatthewStubbs6/big-data-ldn-2017-improving-customer-experience-with-an-ai-bot?ref=https://bigdataldn.com/slides/)

### 什么让机器人变得伟大？

> *   It's not how much "AI" it has
> *   Not how complicated the language model is.
> *   Not whether it uses voice or not.
> *   It is not whether it uses the guided UI or not.

### 是机器人的个性让它变得伟大

## “机器人的个性”到底是什么意思？

老实说，它和我们人类的个性一样复杂。没有一种方法可以定义什么是伟大的人格。这是一系列性格特征的结合。机器人也是如此。伟大的机器人有很多特征。在这篇文章中，我将回顾一下我学到的一些特质。

## 给你的机器人一些个性

首先，给你的机器人取个名字。当我们制造第一个机器人时，我们没有这样做。我们最终让所有人都称它为聊天机器人。但后来我们了解到，只要给它起个名字，就可以让这个机器人变得更生动。现在，当你给它起名字时，想想你希望它是男性角色还是女性角色。

你希望你的机器人给人的印象是机器还是人？这是非常重要的，并且对赋予你的机器人一个角色有很大的帮助。一般来说，具有机器般角色的机器人在对话中大多会非常静态。另一方面，类似人类的机器人会让它们的用户参与生动的对话。

一旦你这样做了，想象一个机器人角色。许多因素将驱动机器人角色——目标客户群的人口统计数据、业务领域和手头的任务，等等。当你对不同情况做出反应时，机器人角色会给你很大帮助。

## # 2——观察说话的语气

> 感谢凯瑟琳·霍恩比的提示。
> 当你为你的机器人选择了一个角色后，决定这个角色如何回应不同的对话是很重要的，比如正式的、非正式的、俚语的使用、口语等等。当做 UX 设计时，我们倾向于将风格指南和色板放在一起，以帮助更广泛的团队提出一致的 UI 设计。机器人也可以做到这一点。你可以让文案为不同的机器人角色编写一份对话风格指南。任何建立机器人对话的人都可以参考这个风格指南，并提出一致的机器人响应。

## # 3——要幽默

幽默在我们身边无处不在，为什么机器人没有呢？当我们对我们的机器人进行第一次内部测试时，很大一部分对话是从用户试图幽默开始的。即使你的机器人是围绕一个不幽默的商业问题构建的，当用户发现他们正在和机器人交谈时，总会有一些人试图淘气，开始对机器人说有趣的事情。你可能不想处理宇宙中的每一种可能性，但对用户的一些有趣的陈述做出幽默的回应会让你的机器人脱颖而出。

## # 4——不要单调

这才是最重要的。随着时间的推移，你的用户会不断回来问类似的问题。通过让机器人以单调的方式回应，很容易建立一个不活跃的对话。想象一下，每次用户开始与机器人对话时，你都表示欢迎。如果你每次都通过说“欢迎鲍勃回来”来欢迎用户，那么很快，这对用户来说将变得非常单调和缺乏吸引力。

如果你使用像[沃森对话](https://www.ibm.com/watson/services/conversation/)或[微软机器人框架](https://dev.botframework.com/)这样的工具，并不需要太多就能带来机器人反应的变化。我们大多数人在开发过程中会忽略这一点，因为这是一个开销。但是不要忘记在你上线或者进行更大范围的测试之前添加这个。不要只为欢迎消息添加变体，而是为从机器人返回的每个响应添加变体。

## #5 -处理闲聊

闲聊相对容易处理，也很容易被忽略。一些像 [Dialog Flow](https://dialogflow.com/) 这样的机器人服务会自动为你处理闲聊。你所要做的就是启用它。但对其他人来说，你需要围绕闲聊来建立对话。如果你在这条船上航行，考虑以下几点

1.  在对话开始时用一条欢迎信息问候用户，并假设用户可能会用诸如“谢谢”、“你好”之类的闲聊来回复。
2.  总是用“再见”、“回头见”等闲聊来结束谈话。并假设用户会用相似单词来响应
3.  消息传递天生缓慢且充满延迟，因此当用户没有及时看到机器人的响应时，他们可能会变得不耐烦。一些用户可能会尝试使用“嗨”、“还在吗？”这样的词与机器人交谈而机器人仍在处理他们之前的请求。最好适当地处理这样的话语。确保你从这些回答中消除了单调。

闲聊是一件永无止境的事情。永远都不够。但是如果你从你的领域角度来看，你可以想出一个合理的小清单，人们可以说机器人可以以友好的方式回复。至于其他的，你可以总是有相同反应的许多变化(记住特性#3 -不要单调)

## # 6——合理处理断章取义的对话

自然语言界面最大的好处之一就是用户可以自由地用他们想要的方式表达。但这也成为我们这些 bot 开发者最大的痛点之一。现在，我们必须处理那些在对话过程中改变对话上下文的用户。让我们看一个酒店搜索机器人
的例子

```
Bot: Welcome to hotel finder. Where are you looking to travel?
User: I am looking for a room for 2 in Dubai next week
Bot: Sure thing. Let me find the best room for you
Bot: What do you think of the following options?
...
User: how long before I can cancel a booking? 
```

Enter fullscreen mode Exit fullscreen mode

对话是在围绕酒店搜索的上下文中进行的。用户询问了一个关于取消的问题。机器人在这里要做的最好的事情是回答用户关于取消的问题(如果建模这样做的话)，然后返回到酒店搜索。[微软 Bot 框架](https://dev.botframework.com/)支持开箱即用的用例。对于其他人，你需要自己做一些工作，但这绝对是可能的。

处理断章取义的对话很重要，因为当用户输入错误导致机器人无法识别用户所说的话时，你需要向用户传达友好的信息，然后回到之前的对话环境。

## #7 -使用情绪分析工具来确定用户的情绪

整个自然语言理解领域仍处于起步阶段。由于技术不够成熟，很难制造出能与人类的说话能力和智慧相匹配的机器人。因此，有时你会让客户不高兴、沮丧或生气。对每个用户声明使用情感分析工具，以了解用户的情绪并做出相应的响应。不要等一个愤怒或沮丧的顾客告诉你他们想和一个人说话。不要忘记询问满意的顾客的反馈。

## # 8——在对话变得对用户太令人沮丧之前移交给一个人

让人类代理更容易接管机器人的对话。让人类很容易从机器人转换到人类(例如，当他们键入帮助时)。让机器人能够轻松地将对话移交给人类代理。

你可能不想从第一天起就把以上所有的东西都放进你的机器人里。但至少给用户一个选择，从机器人切换到人类，开始。分析你的用户是如何与机器人互动的，并决定什么时候获得另外两个功能。

## #9 -提供帮助菜单

最后但同样重要的是，以对话的形式提供一个帮助菜单。你可以做的最简单的事情是，当用户键入“帮助”或“我需要帮助”时，你可以向用户返回一条消息，列出机器人可以帮助用户的所有事情。在这个回答中，你也可以描述如何与人交谈。

同样，[微软机器人框架](https://dev.botframework.com/)提供了开箱即用的特性。对于其他服务，您需要自己构建。