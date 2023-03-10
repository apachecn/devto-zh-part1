# 试水

> 原文：<https://dev.to/yechielk/testing-the-waters>

## 一名新的训练营毕业生正在接受测试。

正如我在[的最新帖子](https://blog.yechiel.me/reactjs-app-with-rails-api-4ffb12ba6608)中提到的，我最近从[熨斗学校](https://flatironschool.com/)的网页开发者训练营毕业。庆祝活动稍微平静下来后，现在需要回答的问题是“现在该怎么办？”

在进行这个项目的过程中，问题总是出现“你毕业后有什么计划？”我们中没有人自欺欺人地认为学习一门课程会神奇地把我们变成 10 倍的 web 开发人员；熨斗在这方面做得很好，把我们变成了一流的学习者，天哪，要学的东西太多了。

每当这些讨论出现时，我总是开玩笑说，毕业后我有一大堆需要学习的事情要做。问题是这是一个横向列表。我想先做所有的事情！
[![ALL THE THINGS!](img/20e298d932808c2f5933669cc7a6f551.png "And do them first!")](https://res.cloudinary.com/practicaldev/image/fetch/s--a7UDK6uL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2AnfM9x8kN_5RPlbZE98seNA.jpeg) 
所以现在我毕业了，所有的玩笑都结束了，我必须选择我的下一个重点将是什么(当然除了为我的下一个开发职位找工作)。我应该专注于用算法和数据结构来复习基本的 CS 技能吗？我应该继续学习一门新的语言/框架吗？

最后，我决定专注于编写测试。

### **为什么要考试？**

让我首先向我的非技术观众解释一下我所说的测试是什么意思。

假设你在做一个网站。为了这个思想实验，让我们假设这个网站所能做的就是允许用户注册、登录、注销和删除他们的账户(就像脸书，但是没有烦人的部分)。

比方说，我花了一个小时才完成，现在我必须确保它能正常工作。这应该不会太复杂，我的网站只能做 3 件事，所以我注册了一个测试用户，注销，用我刚刚创建的用户重新登录，然后删除它。完美，一切顺利，在预算之内，提前完成。

现在我想让我的网站做点别的；也许用户应该能够编辑她的信息。所以我添加了这个功能，为了确保这个新功能正常工作，我用我的测试用户登录，并尝试编辑用户名。那也可以，但是等等，我还想确保在编写“编辑用户”特性时没有破坏其他任何东西，所以我回到我的应用程序，注册一个新的测试用户，注销，用一个现有用户重新登录，编辑信息，然后删除用户。在这个过程中，我意识到不知何故出现了一个破坏“删除用户”功能的错误，所以我回到代码中修复它，为了验证一切都已修复，注册一个新的测试用户，注销，用现有用户重新登录，编辑信息，然后删除用户。

不难发现，这个过程会很快变得非常耗时，而且随着我在网站上添加更多功能，情况只会变得更糟。

### **进入测试**

解决方案是自动化流程。对于我做的每个项目，我都会添加一个测试套件。因此，对于我上面的应用程序，我的测试套件将包含一个脚本，用于注册用户、让现有用户登录等等。

理想情况下，我的目标是让测试覆盖我的应用程序中的每个特性，这样每当我对代码进行任何更改时，我所要做的就是运行测试，在几秒钟内我就可以确认我没有破坏任何东西，如果我破坏了任何东西，我就可以确切地知道它在哪里被破坏了，这样我就可以修复它。

在熨斗，我们处理了很多测试，我们阅读它们，运行它们，当它们失败时，我们敲我们的头，当我们通过它们时，我们庆祝。
[![CELEBRATE GETTING THAT LEARN GREEN!](img/e4962f1b90e27691323721e029613670.png "This GIF used to be real popular on our Flatiron Slack")](https://res.cloudinary.com/practicaldev/image/fetch/s--1VVFgLc6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ASTzY3STu64_HqzD9KiCuOQ.gif) 
事实上，大部分的学习并不是通过讲座或书本完成的，而是通过一种我称之为 TDL 的模式(测试驱动学习，松散地基于 TDD，测试驱动开发)完成的。我们将从克隆一个 GitHub repo 开始，运行测试套件，分析错误消息，并做必须做的事情来让它们通过。
[![Passing Tests](img/a530dae5513931d2908acc5fd7e7d14e.png "Fellow Learners are known to wake up in middle of the night screaming about this lab…")](https://res.cloudinary.com/practicaldev/image/fetch/s--S0nehM8_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Ad-UV-tZNlcMc47ScDWGFMQ.png) 
我们没怎么做的一件事，其实就是写测试。我认为这是不幸的。编写测试是编写软件如此重要的一部分，花些时间学习如何正确地编写它们会很好。

在我的上一个项目中，我确实自己写了一些测试，但远没有我希望的那么多。所以现在我“靠自己”了，我决定改变这种状况。在接下来的一两个星期里，我将阅读测试，给我的旧项目添加一些，甚至可能从头开始写一个完整的 TDD 应用程序。

基于一些朋友的推荐，我开始通读 [Rails 4 测试处方](https://pragprog.com/book/nrtest2/rails-4-test-prescriptions)，如果任何人读到这里有更多的建议，请在评论中留下。

一如既往，编码快乐！

* * *

*这篇文章是从我的博客 [Coding Hassid](https://blog.yechiel.me/testing-the-waters-79cf15e3bbe9)
交叉发布的，你可以在那里阅读更多关于我的编码之旅，或者在 Twitter 上关注我 [@yechielk](https://twitter.com/yechielk)*