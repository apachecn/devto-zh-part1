# 我选择打字稿是为了怀旧

> 原文：<https://dev.to/aunyks/i-chose-typescript-for-the-nostalgia>

当我 13 岁开始读高一的时候，我不确定我想先学习哪种编程语言。在浏览了很多论坛和 YouTube 上的一些旧视频后，人们几乎总是推荐 Java 作为实用且有利可图的第一语言。所以，学到的就是这些。

快进到大三，我已经有了相当多的类文件、NullPointerExceptions 和各种编译错误。我正在轻松地通过我的 AP 计算机科学课程(正如你所猜测的，该课程教授 Java)，并且我开始研究开源我的工作并构建更多的实用项目。

我决定用 Java EE 和 Apache Tomcat 编写一个 web 服务器，由于我厌倦了编写更多的语法而不是语义，可以说我对 Java 感到非常失望，所以我冒险进入了一个不同的生态系统。

经过一番谷歌搜索，我发现了 Node.js 和 [Express](https://expressjs.com) 。在看到一个 Hello World 服务器的例子后，我爱上了这些技术提供的简洁性和抽象性。

```
var express = require('express')
var app = express()

app.get('/', function (req, res) {
  res.send('Hello World!')
})

app.listen(3000, function () {
  console.log('Example app listening on port 3000!')
}) 
```

Enter fullscreen mode Exit fullscreen mode

因此，我差不多成了一名 JavaScript 开发人员。我开始学习 JS 为新开发者提供的回调、原型和其他很酷的东西。我开始在各种项目中使用 Javascript:前端、后端、桌面、命令行等等。我爱上了 JavaScript 的无处不在和简单性(动态键入和不必使用分号看起来太棒了…起初)。

但是，现在我高中毕业了，我已经写了几千行 JavaScript 代码。最初让我喜欢上这门语言的东西现在成了我不喜欢它的理由。我开始看到动态类型、不严格的语法和语言的其他方面的缺陷。我开始寻找一种编译成 JS 的语言来保持我的信念，在我(可能)离开生态系统之前。Java 的语法和类型从未如此吸引人…

就在那时，我发现了 [TypeScript](https://typescriptlang.org) ，一个 JavaScript 的*类型化*超集。来自 ES5，我喜欢 TypeScript 自动包含 ES6 和超越的特性(像那可爱的、可爱的`class`语法),而不需要复杂的 Babel 配置。在这一点上，我很高兴看到一些让我想起 Java 时代的语法。

但是，ES6 本身并没有提供强大的类型功能:这就是 TypeScript 真正的优势所在。就像 Java 的类型是实施一样，TypeScript 的类型是建议。因此，您有机会看到类型在语义上如何在整个应用程序中流动，类似于 Java 和 C#等编译语言，但您不必遵守类型不一致，因为它仍然是 JavaScript。对我来说这就足够了。

如你所知，语法本质上是 JavaScript，除了[类型声明文件](https://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html)。看看下面的示例代码。借自[本页面](https://tutorialzine.com/2016/07/learn-typescript-in-30-minutes)。

```
var burger: string = 'hamburger',     // String 
    calories: number = 300,           // Numeric
    tasty: boolean = true;            // Boolean

// Alternatively, you can omit the type declaration:
// var burger = 'hamburger';

// The function expects a string and an integer.
// It doesn't return anything so the type of the function itself is void.

function speak(food: string, energy: number): void {
  console.log("Our " + food + " has " + energy + " calories.");
}

speak(burger, calories); 
```

Enter fullscreen mode Exit fullscreen mode

就像我之前说的，它看起来就像普通的 JS，除了你可以选择用冒号语法定义每个变量和函数返回的类型。不麻烦！😉

虽然 TypeScript 的类型更多的是一种推荐，但它足够接近于提醒我一种更严格类型化的语言，如我的起点 Java。通过添加类型，它有助于在构建大型项目和应用程序时提供一点所需的结构，这是 C#、Java 和 C++等语言得以使用的原因。

虽然我已经厌倦了编译语言对开发人员的冗长要求，但事实证明，我可能已经把它视为理所当然。当给予动态类型、可选分号和其他工作自由时，由于宽松，构建和调试大型应用程序似乎变得明显更难了。TypeScript 的静态类型幻觉提供了保持代码整洁和可读性所需的结构。如你所见，我选择了 TypeScript 来怀旧。你会选择它做什么？

[![Thank you](img/b43709364925eb0e0c9a1c62f738f5c7.png)T2】](https://i.giphy.com/media/3otPoUkg3hBxQKRJ7y/giphy.gif)

感谢你阅读这篇文章！请随时给我一些反馈，告诉我你是否会使用 TypeScript，为什么。

[Twitter](https://twitter.com/aunyks) ， [Github](https://github.com/aunyks) ， [Snapchat](https://snapchat.com/add/aunyks) ， [Medium](https://medium.com/@aunyks) ， [Instagram](https://instagram.com/aunyks)