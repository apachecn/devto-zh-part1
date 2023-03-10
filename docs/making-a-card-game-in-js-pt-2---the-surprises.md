# 在 JS，pt 制作一个卡牌游戏。2 -惊喜

> 原文：<https://dev.to/andrew565/making-a-card-game-in-js-pt-2---the-surprises>

这是我根据 Matthew Lowes 的[地牢纸牌](https://matthewlowes.com/games/)创作纸牌游戏系列的第二部分。你可以在这里找到这个系列[的第一部分](https://andrewscripts.wordpress.com/2017/03/16/how-to-make-a-card-game-in-javascript-part-1/)。

## 惊喜

自从我在这个系列中的第一篇文章以来，我遇到了一个非常奇怪的事件，导致我一直在工作的设置分崩离析，以至于我不能再使用它。因此，这篇文章将简要地关注我在这个过程中所做的一些后端基础设施的改变。

## 开始的时候，有一点小故障，但是很好

从一开始，我就设想这个游戏是一个单页应用程序(SPA)。这个游戏不需要数据库的支持，也没有复杂到前端无法处理的计算。知道了这些事情，我决定在 [Glitch](https://glitch.com/about/) 中启动这个应用程序，这是一个允许你在浏览器中编码的在线 IDE。虽然有很多东西让 Glitch 变得很棒，但我最喜欢的是实时重新加载和自动保存相结合，这样你就可以立即获得一个在线的最新应用程序，你只要写一行代码就可以测试它。(重新混合别人的应用程序，这样你就不用自己做所有的样板文件，这也是非常非常好的。)

这是一个很好的设置，直到我遇到了两个问题，这两个问题与我的关系比与 Glitch 本身的关系更大。第一个问题是由于不稳定的网络连接导致的间歇性存储问题。给你一个忠告:永远不要在公共无线网上做任何重要的事情。这是可以忍受的，直到我为此损失了 20 多分钟的工作时间。

第二个问题是 ES6/ES2015 支持。我希望这款应用能像普通 JS 一样前沿，所以我想利用 ES2015 的所有功能。Glitch 内置的 ES2015 支持仅限于 Node 支持的任何东西，这意味着尽管大多数东西都可以工作，但我无法利用`import`或`export`语句来使我的类模块化(这要追溯到 Node 添加模块支持之前)。虽然有变通办法，但我决定，如果我要去所有的麻烦，我可能会更高兴只是在本地运行它。

## 它是一个代码包——直到我破解它

我最喜欢的运行本地项目的工具是 [CodeKit](https://codekitapp.com/) 。这是最容易使用的工具之一，用于处理所有挑剔的后端服务器和预处理程序，它们的配置和设置很烦人。把它想象成一个 GUI 版本的 Grunt 或 Gulp。通常情况下，效果很好。但是因为我不能完全不管，当我试图让导入/导出关键字工作时，我试图弄乱一个太多的设置，结果破坏了一些糟糕到我再也不能让它工作的东西。同样，我肯定这是另一个 PEBKAC 错误，但我再次决定，如果我要大费周章地尝试并修复我所做的事情，我还不如咬紧牙关，完全用手工编码的任务运行器设置重新开始。

## **更现代的设置**

因为我几乎从来没有从零开始过，所以我决定看看别人推荐的教程。我找到了 Gina Trapani 的这篇文章——它反映了美国 web 开发人员在 JS 开发这个移动目标上的许多挫折。那篇文章给[带来了这份内容广泛的指南](https://github.com/verekia/js-stack-from-scratch)，作者是 Jonathan Verrecchia(在 Yelp 工作)，我尽可能密切地关注着它。

让我大吃一惊的一步是通过 ESLint 在我的代码上启用 Airbnb 的 styleguide。我认为自己是一个非常优秀的开发人员，所以我对自己代码中的粗心大意感到震惊。

## 结束了一切都好...

好吧，现在我又回到正轨了。下次我将回到手头的实际代码！