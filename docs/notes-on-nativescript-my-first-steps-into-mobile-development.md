# Nativescript 笔记:我进入移动开发的第一步

> 原文：<https://dev.to/nickp60/notes-on-nativescript-my-first-steps-into-mobile-development>

# 简介

## 背景

几年前，当我开始从一名湿实验室科学家转变为一名计算生物学家时，我开始听 [Programming Throwdown](http://www.programmingthrowdown.com/2016/11/episode-60-javascript-for-mobile.html) 。我把我的移液器、离心机和培养皿世界换成了我信赖的旧 thinkpad，慢慢地学习成功所需的行话和技能。PT 帮助我熟悉了编程世界中的许多语言和生态系统。去年，他们播放了一集关于 Nativescript 的内容，我对此很感兴趣。由于我不了解 Swift 或 Java，我认为移动开发不适合我，但他们对 Nativescipt 的描述激起了我的兴趣。我真的可以只用 Javascript 和 XML 制作移动应用吗？我决定试试。

## 第一件事

也就是说，我对 JS 或 XML 一无所知。我阅读了[雄辩的 Javascript](http://eloquentjavascript.net/Eloquent_JavaScript.pdf) ，并在阅读了 [Kamil 的帖子](https://dev.to/corgibytes/setting-up-a-minimal-yet-useful-javascript-dev-environment)后建立了一个最小的开发环境，并慢慢开始构建我心目中的应用程序背后的逻辑(本质上，是一个多年前实验室计算电子表格的替代品)。我开始喜欢用 JS 编程；有很多次我被奇怪的类型转换所困扰，但总的来说，它与我已经熟悉的语言(Python、R、C)非常相似，不会太复杂。在线支持的数量是惊人的。

以下是我对自己经历的一些简单反思，以及我对任何想开始的人的建议。

注意:我绝不是专家；我只想分享我作为一个非专家、JS 新手、对移动一无所知的门外汉使用 Nativescript 的经验。

# 原生脚本课程

## 第一课:避免选择的悖论

在我使用过的所有框架中，Nativescript 拥有一些最好的文档。很明显，它已经运行了最小的例子，并带你通过一个越来越复杂的购物清单应用程序来找到窍门。一切都进行得很顺利，所以为了投入进去，我克隆了一个[演示应用](https://github.com/gethuman/nativescript-sidedrawer/tree/master/demo)，它使用了我脑海中的抽屉导航系统。

这就是我注意到有趣的地方。这个演示应用程序是用 Typescript 编写的！没问题，我想，我会把它 javascript 化，然后继续。但是，这是第一次这样的“啊？”时刻。

Nativescript 的优点之一是它的灵活性。可以用 TypeScript 或者 JavaScript 写。你可以用不同的方式来组织这个项目。它可以通过插件和模块无限扩展。然而，这使得寻找模板例子变得很棘手。通常，如果你试图从一个有趣的插件中实现一个最小的例子，你会发现它需要大量的修改才能工作。因此，在开始之前，尽可能多地查看高质量的示例性回复:找到对你最有意义的一个，然后一头扎进去。

## 第二课:给猫剥皮的多种方法

与上一课类似，Nativescript 本身非常灵活。当我第一次开始时，它看起来足够简单:js 用于逻辑，xml 用于结构，css 用于样式。能有多难？

在开发我的应用程序一段时间后，我了解到这种理解是现实的简化版本。如果您愿意(一旦看到它的帮助，您可能会这样做)，您可以在 xml 表中设置样式，在 js 中设置页面的结构。你为什么要这么做？简单地说，它允许您将应用程序逻辑绑定到页面的布局，这样您就可以根据某些情况动态地更改 UI。这是一个不可思议的有用特性，但是(我觉得)在文献中没有很好的解释。

这导致了很多“啊？”看例子的时刻。Nativescript 文档确实有不同做事方式的例子，但是教程的方式并不明显。例如，看看下面的例子，从文档中制作一个网格布局。

xml:

```
<GridLayout columns="80, *, auto" rows="auto, *" >
 <Button col="0" />
 <Button col="1" />
 <Button col="2" />
 // by default column and row are set to 0
 <Button row="1" colSpan="3" />
</GridLayout> 
```

Enter fullscreen mode Exit fullscreen mode

或者，js:

```
//var layout = require("ui/layouts/grid-layout");
var gridLayout = new GridLayout();

var btn1 = new Button();
var btn2 = new Button();
var btn3 = new Button();
var btn4 = new Button();
gridLayout.addChild(btn1);
gridLayout.addChild(btn2);
gridLayout.addChild(btn3);
gridLayout.addChild(btn4);

GridLayout.setColumn(btn1, 0);
GridLayout.setColumn(btn2, 1);
GridLayout.setColumn(btn3, 2);

GridLayout.setColumnSpan(btn4, 3);

// ItemSpec modes of the column refers to its width.
// Absolute size of the column
var firstColumn = new ItemSpec(80, GridUnitType.pixel);
// Star width means that this column will expand to fill the gap left from other columns
var secondColumn = new ItemSpec(1, GridUnitType.star);
// Auto size means that column will expand or shrink in order to give enough place for all child UI elements.
var thirdColumn = new ItemSpec(1, GridUnitType.auto);

// Star and Auto modes for rows behave like corresponding setting for columns but refer to row height.
var firstRow = new ItemSpec(1, GridUnitType.auto);
var secondRow = new ItemSpec(1, GridUnitType.star);

gridLayout.addColumn(firstColumn);
gridLayout.addColumn(secondColumn);
gridLayout.addColumn(thirdColumn);
gridLayout.addRow(firstRow);
gridLayout.addRow(secondRow); 
```

Enter fullscreen mode Exit fullscreen mode

很难看出 js 示例中的内容。xml 非常清楚:用一些行和列定义一个网格，并在那里放置 4 个按钮。

js 做完全相同的事情，但是是从 js 文件而不是 xml 文件。

这很酷，但很难立即看出发生了什么。我发现当我试图重用代码示例时，很多时间都花在了搞清楚发生了什么上。

## 第三课:选择一个绑定方案并坚持下去。

我没有 web 背景，所以我不熟悉 Nativescript 试图解决的 DOM 问题。我发现 Nativescript 中可用的绑定方案非常令人困惑。再说一遍，[文档](https://docs.nativescript.org/core-concepts/data-binding)很好，但是要理解静态、单向、双向、父母、子女、继承等等发生了什么，还需要一些认真的修改。更复杂的是不同的人处理问题的方式。

我遵循了杂货应用为我的应用 0.1 版本设置的方案。但是随着事情变得越来越复杂，我遇到了这个指南，它重新定义了我如何看待绑定模型。该指南中的那个对我来说更有意义(我需要事件实时更新，而不需要等待“提交”事件)，所以我最终重写了许多以前的逻辑，以反映下一版本的新模型。

# 结论

综上所述，在开始之前，请大量阅读并研究使用 Nativescript 的不同方法。我开始时对自己正在做的事情知之甚少，最后随着我对框架的理解的更新，我花了很多时间来修改东西。也就是说，这是一个很棒的框架，我期待将来在其他项目中更多地使用它！