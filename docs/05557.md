# 我需要学习什么才能在 Clojure 中被雇佣？

> 原文：<https://dev.to/ericnormand/what-do-i-have-to-learn-to-be-hirable-in-clojure>

来自一位读者:

> 我目前是 Ruby 的一名软件开发人员，但我很想在 Clojure 工作，尽管我还是个初学者。不幸的是，我认为我的公司不会很快转向 Clojure。而且我也没有太多空闲时间去做 Clojure。要想在 Clojure 中被录用，我需要学习些什么？

这是一个棘手的问题。这是一个棘手的问题，因为从技术上来说，你需要零 Clojure 知识才能在 Clojure 找到工作。我知道这一点，因为我刚刚遇到了一个人，他刚从 Rails 实习中被录用到现有的 Clojure 代码库中。他完全没有犯罪的经验。

我和很多在 Clojure 工作并正在为 Clojure 招聘的人交谈过。这些工作有两大类经验要求。

### 1。他们想要有经验的 Clojure 程序员。

出于几个原因，你会找到这样的工作。有时候，他们希望**替换一个即将离开**的有经验的程序员。有时他们刚刚起步，想要**立即投入运营**。我也见过 CTO 喜欢 Clojure，但希望**有比他们更好的人**，因为 Clojure 是新领域。

对于这些工作，他们真正寻找的是具有扎实法律技能的人。但是有一个秘密我会在这篇文章的最后揭露。

### 2。不需要 Clojure 经验。

这一类比较有意思。你会经常听到他们说他们在**【寻找优秀的人】**。他们知道在 T2 很难找到有经验的 Clojure 程序员。

对于这些工作，他们愿意投入时间和金钱，让你成为一名 Clojure 程序员。学习 Clojure 会给你报酬，通常是和导师一起工作。

Clojure 经验当然会有帮助。我只能想象第二种类型得到更多的应用，所以你会想要脱颖而出。但是，您仍然不需要 Clojure 经验。这里有一些能让你脱颖而出的东西。这些大致按照从最强大到最弱小的顺序排列。

### 1。Clojure 体验。

Github 上的**【完成】 <sup id="fnref2">[2](#fn2)</sup> 项目。**

这些用不了多长时间。它们不需要很复杂。只要表明你能用 Clojure 让事情发生。

的*奖励积分*

*   [使用数据库](https://purelyfunctional.tv/web-dev-in-clojure/making-a-complete-app/)
*   [点击外部 API](https://purelyfunctional.tv/clojure-in-one-hour/clojure-twitter-bot/)
*   [使用多线程](https://purelyfunctional.tv/courses/concurrency/)
*   [部署到 Heroku/a 服务器](https://purelyfunctional.tv/web-dev-in-clojure/lets-get-a-server-up-and-running-in-the-cloud/)
*   用 clo jure+[ClojureScript](https://purelyfunctional.tv/clojure-in-one-hour/re-frame-in-one-hour/)编写一个 [web app](https://purelyfunctional.tv/clojure-in-one-hour/luminus-in-one-hour/)

### 2。其他口齿不清

至少，这表明你知道你要做什么。甚至在 SICP 的大学课程或一些完整的练习都会有所帮助。

的*奖励积分*

*   [宏](https://purelyfunctional.tv/courses/clojure-macros/)
*   解决有趣的问题(用 GitHub 上的代码！)

### 3。其他函数式编程经验

Haskell、Erlang、Scala - heck，甚至函数式 JavaScript 或 Java 8 流都会让人印象深刻。

的*奖励积分*

*   了解你的 [3 个功能工具](https://purelyfunctional.tv/courses/3-functional-tools/)(映射、过滤、减少)
*   使用[不可变数据结构](https://purelyfunctional.tv/courses/modeling-solitaire-in-clojure/)对问题建模

### 4。JVM 体验

Clojure 运行在 JVM 上，其中有许多关于对象如何工作、JVM 如何部署的小细节，以及所有在编写 Clojure 时会派上用场的东西。它能帮你脱颖而出。

的*奖励积分*

*   [部署 JVM 应用程序](https://purelyfunctional.tv/article/jvm-deployment-options/)
*   除了 Java 之外还使用 JVM 语言
*   用 Java 8 流编程
*   配置 JVM

### 5。JavaScript 体验

如果您打算在这项工作中使用 ClojureScript，了解浏览器、DOM API 以及 JavaScript 部署的所有问题将会有所帮助。

的*奖励积分*

*   使用其他[编译成 js 语言](https://github.com/jashkenas/coffeescript/wiki/list-of-languages-that-compile-to-js)
*   构建[反应](https://facebook.github.io/react/)应用程序(特别是使用[冗余](http://redux.js.org/)
*   使用[不可变的. js](https://facebook.github.io/immutable-js/) 或[森](http://swannodette.github.io/mori/)

### 6。多种语言

如果你打算在工作中学习一门新的语言，证明你确实可以做到。最好的候选人至少有 4 种语言，并有多种范例。

的*奖励积分*

*   两个或多个[范例](https://en.wikipedia.org/wiki/Programming_paradigm) (OOP、FP、过程、逻辑)
*   两个或更多的语法家族(C vs Python vs Ruby vs Haskell)

经过这一切，人们没有被雇用的首要原因是他们没有申请足够的工作。把你的简历放在一起，在你的工作/学校/兼职项目记忆中跋涉，从上面的列表中找出一些事情来强调。然后申请很多工作，在求职信和面试中提到这些。

我之前暗示过的一个小秘密是，即使是寻找有经验的 Clojure 程序员的工作也经常会雇佣那些乐于学习的聪明人。需求只是一个建议。所以去申请吧！

以下是 Clojure jobs 的好来源。

唯一的另一件事是，找工作也仍然与你认识谁有关。关于这一点有很多要说的，而且你已经获得了比你可能知道的更多的优势。

* * *

1.  说真的，我从没想过在新奥尔良会有多家公司为 Clojure 招聘员工。这些都是在没有我的情况下发生的。 [↩](#fnref1)

2.  如果你像我一样，项目永远不会结束。但是一个“完成”的项目完成了一些目标。很有用，很管用。 [↩](#fnref2)