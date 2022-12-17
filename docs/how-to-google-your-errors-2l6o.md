# 如何谷歌你的错误

> 原文：<https://dev.to/swyx/how-to-google-your-errors-2l6o>

所以你是一个尝试新技术的新手，正在考虑自己的事业，突然:

```
Uncaught (in promise) TypeError: _super.call is not a function at new ObservableQuery (b1f6a7d9f98d979758232d0dc3c394ce.js:26213)
    at QueryManager.watchQuery (b1f6a7d9f98d979758232d0dc3c394ce.js:27305)
    at b1f6a7d9f98d979758232d0dc3c394ce.js:27332
    at new Promise (<anonymous>)
    at QueryManager.query (b1f6a7d9f98d979758232d0dc3c394ce.js:27330)
    at ApolloClient.query (b1f6a7d9f98d979758232d0dc3c394ce.js:27981)
    at Object.require.2.react (b1f6a7d9f98d979758232d0dc3c394ce.js:29740)
    at newRequire (b1f6a7d9f98d979758232d0dc3c394ce.js:41)
    at require.39 (b1f6a7d9f98d979758232d0dc3c394ce.js:66)
    at b1f6a7d9f98d979758232d0dc3c394ce.js:71 
```

Enter fullscreen mode Exit fullscreen mode

你把整件事复制粘贴到谷歌上，仅仅一秒钟后:

```
Your search - Uncaught (in promise) TypeError: _super.call is not a function - did not match any documents.

Suggestions:

    Make sure that all words are spelled correctly.
    Try different keywords.
    Try more general keywords.
    Try fewer keywords. 
```

Enter fullscreen mode Exit fullscreen mode

或者更糟，你得到了大量的结果，但是没有一个是有用的！

现在怎么办？

* * *

这是布列塔尼·斯托罗斯今天提出的问题。作为一名 Javascript 新手，我对此非常熟悉。Javascript 不是一种类型安全的语言，所以它特别容易出现神秘的错误。在过去的一年里，我已经用谷歌搜索了这个错误，并取得了不同程度的成功，所以我将在这里列出一些想法(以及结合其他人的想法)。开始了。

[![Alt text of image](img/0a294d3d89f68874c5c299d30287515b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GVnvl2dh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://geekifyinc.com/wp-content/uploads/2017/09/IMG_0333-1280.jpg)

## 1。不要惊慌

[不要慌](https://en.wikipedia.org/wiki/Phrases_from_The_Hitchhiker%27s_Guide_to_the_Galaxy#Don't_Panic)！😂谷歌搜索错误是一种成年礼。事实上，把它当作一个受欢迎的机会来*练习*研究错误，因为这是你职业生涯中的一项关键技能。这个过程可能会发现你不知道自己遗漏了的其他知识。

现在你冷静了:**阅读错误**。引用马克·埃里克森的话:

> ![Mark Erikson profile image](img/fd17f2b23ea5c2de6740f9b6c480866b.png)马克埃里克森@阿塞马克![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)轻微一点发泄:
> 
> 错误信息 _ 通常 _ 有相关信息。你可能不明白每一个词，但他们经常会告诉你问题是什么，以及如何解决。即使谷歌搜索不到这条信息，你可能已经有了解决它所需的所有信息。03:42am-02 2018 年 1 月[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=948036804975955968)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=948036804975955968)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=948036804975955968)

信不信由你，有人在某个地方努力写出了你正在阅读的那个错误。这有意义吗？还是你只是在盲目的恐慌中对此视而不见？

## 2。橡皮鸭

[橡皮鸭调试](https://en.wikipedia.org/wiki/Rubber_duck_debugging)是软件工程久经考验的方法。基本上，尝试用自己的话向一个无生命的物体(最好是可爱的，吱吱叫可选)解释你在尝试做什么，错误是什么。你对问题的自然语言描述很可能是其他人也将如何描述它，所以把它输入谷歌，看看会发生什么。你可能会感到惊讶！🦆

## 3。不断扩大圈子(删除无关信息)

您的错误可能包含许多特定于应用程序的信息。例如:

```
$ node_modules/.bin/parcel watch app/client/entry.html --out-dir public/dist
🚨  Cannot read property 'type' of undefined
    at Bundler.createBundleTree (/home/ben/projects/dg/node_modules/parcel-bundler/src/Bundler.js:373:52) 
    at Bundler.createBundleTree (/home/ben/projects/dg/node_modules/parcel-bundler/src/Bundler.js:412:12) 
    at Bundler.createBundleTree (/home/ben/projects/dg/node_modules/parcel-bundler/src/Bundler.js:412:12) 
    at Bundler.buildQueuedAssets (/home/ben/projects/dg/node_modules/parcel-bundler/src/Bundler.js:245:23)                                                                                                           
    at <anonymous>                                   
    at process._tickCallback (internal/process/next_tick.js:188:7) 
```

Enter fullscreen mode Exit fullscreen mode

把所有这些都输入谷歌不会有任何帮助！为什么？因为你留下了一堆假设，只有你会用到，例如`app/client/entry.html`和`/home/ben/projects/dg`。因此，删除它们并降低搜索的特异性可能会改善搜索结果，以找到与你有类似问题的其他人。

詹姆斯·罗说得比我更好:

> ![james_roe profile image](img/2b23495c41efb026b5d8cee0f30b15f0.png)詹姆斯 _ 罗@詹姆斯 _ 罗![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)[@ brittanystoroz](https://twitter.com/brittanystoroz)错误代码- >错误消息- >语言- >库- >语法将是我的总指南，在 descendi…[twitter.com/i/web/status/9…](https://t.co/ywJ0lMRHdW)00:02AM-04 2018 年 1 月

所以如果你有一个错误代码，谷歌一下。如果这不起作用，谷歌错误信息。如果这不起作用，谷歌一下你正在使用的图书馆。诸如此类！

## 4。给出更多上下文(添加相关关键词)

错误还因其遗漏了 T2 而引人注目。在这种情况下，有一个隐含的假设，即您，即开发人员，知道您正在使用什么语言或库。但是谷歌是不可能知道的！通过添加你正在使用的技术栈作为关键词来帮助谷歌，例如`parceljs Cannot read property 'type' of undefined`。请记住，你的最终目标是希望找到一个 Github 问题、Stackoverflow 问题或博客帖子，所以请尽可能向 Google 提供所有的背景信息，帮助它找到你需要的答案！

顺便提一下，**版本号**也是上下文的一个重要部分。如果你在 D3.js v4 上遇到错误，那么 D3.js v3 的回答不会有太大帮助！如果你没有版本号或者认为你的错误是一般性的，那么对你的查询设置一个日期限制(Google 允许你限制在过去的一年内)很可能会找到更相关的结果。

## 5。使用高级搜索运算符

如果你知道如何使用的话，谷歌的搜索框功能强大。查看[这张备忘单](https://support.google.com/websearch/answer/2466433?hl=en)(或[其他](https://www.lifewire.com/advanced-google-search-3482174)如[这张](https://bynd.com/news-ideas/google-advanced-search-comprehensive-list-google-search-operators/))的高级搜索提示，只搜索引用的内容，或搜索所有的搜索词，等等。(感谢 [Guinivere Saenger](https://twitter.com/guincodes/status/948740376705187840) )。特别的奖励，这也为你的非开发生活增加了你的 Google-fu！

## 6。不要谷歌！

谷歌不是唯一的搜索引擎。人们可以去很多地方寻求帮助——比如 Stackoverflow 和 Github——它们也有很多很好的搜索功能！

谷歌也不是对开发者最友好的搜索引擎。引用昆西·拉森的话:

许多新程序员没有意识到的另一件事是，谷歌在其查询中忽略了大多数非字母数字字符。程序员一直用的符号喜欢！@#$%^ &和*没有被搜索到。都不是(){}[]，"

所以如果你在搜索中有很多符号，使用 [DuckDuckGo](https://duckduckgo.com/) ！

## 7。想想你最大的未知

编程是按照抽象层来工作的。在最大的宏观层面上，有 [OSI 模型](https://en.wikipedia.org/wiki/OSI_model)，但是如果你是一个应用开发者，那么你的层可以是这样的:

*   语言
*   环境
*   结构
*   图书馆
*   应用

我说的最大未知是什么意思？嗯，如果你是一门**语言**的新手，那么当你有错误时，你很可能犯一个简单的语法错误，因为你还不熟悉一门新语言可能带来的所有语法和边缘情况。

自信不是语言问题？然后进入**环境**。语言是不断发展的规范，你编码的环境对于你实际上如何使用语言很重要。作为一名 Javascript 开发人员，我总是看到[人们被他们是否能使用 ES6 语法](https://forum.freecodecamp.org/t/changing-js-version/166453/3)所困扰。所以他们会得到错误，然后谷歌一下，完全被弄糊涂了。

你已经排除了环境因素？进入**框架**。如此等等。如果适合你，你甚至可以逆序进行。什么都行。

这一步并不意味着要花很长时间，我只是用了冗长来强调我所说的“思考你最大的未知”是什么意思。你最大的未知是你最大的风险来源，因此是第一个(但不是唯一的！)有错误时可以查看的地方。

## 8。阅读文件

如果您的错误与某个特定的框架或库有关，那么很可能有一些您可能不知道的概念或语言是您的错误的隐藏原因。去读读文档，看看例子，理解你和那些例子有什么不同。注意文档中特定的词语选择，并将其作为关键词添加到你的谷歌搜索中，看看是否会出现更好的结果。

## 9。重现错误

开始一个全新的项目，把它做得非常小，这样你就可以隔离你的错误。从你现有的项目中复制最少的错误，或者试着从零开始编码，而不需要你的主项目中所有多余的东西。

如果你不能重现错误，你会发现一个巨大的线索，关于你的错误是怎么回事。

如果你能重现错误，那也很好，因为那会让你...

## 10。寻求帮助

到处张贴你的错误。Github，Stackoverflow，Reddit，Twitter，Slack/Discord 社区， [Dev.to](https://dev.to/t/help) (咳咳！)，你说吧。

如果您从第 9 步中获得了最低重现性的样本，那么它将有助于人们了解正在发生的事情。

此外，它将帮助未来的人谁有你的确切错误能够谷歌找到你。如果我们想要有谷歌式的错误解决方案，就必须有人来开个头！

# 更多资源

*   迈克尔·霍夫曼关于[如何被卡住的演讲](http://code-worrier.com/how-to-be-stuck)
*   作为一名程序员，我如何掌握谷歌搜索的艺术？

祝你好运！