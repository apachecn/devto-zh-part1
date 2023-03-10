# 重构、重写和领导攀登

> 原文：<https://dev.to/jskulski/refactoring-rewriting-and-lead-climbing-1689>

*最初写于 2014 年，为开发人员再版至*

我已经多次使用了单词 *refactor* 。我是这样的:

> "接下来的三周你打算做什么？"
> 
> “我要重构登录系统”

米（meter 的缩写））Fowler 在他的精彩著作 *Refactoring 中，*给出了*的定义“对软件内部结构的改变…而不改变其可观察的行为”*

所以技术上来说，好吧。去吧，我，*重构*登录系统三周。但是用词和准确性很重要。或许我应该说我是在*重写*登录系统。

福勒也给出了这样的定义:

> 应用一系列重构，而不改变其可观察的结构。

这个定义让我更加成功。更安全更温暖更舒适。如果这是一个没有发布到产品的数周过程，我称之为*重写。*和*重写*很难，很可怕，风险也更大。所以我尽量避开他们。

[![Lead climbing. Many anchor points, several discrete releasable points.](img/52e90dc4b4c8d78067fe012f1a437450.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mhOT2Pto--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AmxRLCJM4fEFh0hTXn49f1w.gif) 
引攀。许多定位点，几个离散的可释放点。

当我将重构视为一系列提高代码质量的*离散*步骤时，我会采取这些小步骤。我所说的离散，是指可发布到生产中。

提取一个方法。创建一个小班。重命名变量。添加类型提示。朝着正确的方向做一个小的、安全的、自信的承诺。

一小步可以带我们走很长的路。