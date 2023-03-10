# 使用 Canvas 和 ES6 类

> 原文：<https://dev.to/washingtonsteven/playing-with-canvas-and-es6-classes>

*这也可以在我的新博客上找到[http://Washington Steven . net/playing-with-canvas-and-es6-classes/](http://washingtonsteven.net/playing-with-canvas-and-es6-classes/)*

## 坐火车

在刚刚过去的劳动节周末，我和妻子花了一些时间乘火车去纽约市看风景，吃一些美食，并且通常会被这座城市的极度密集所淹没。这是一个伟大的城市，我知道很多人喜欢它，但它越来越不是我的风格。然而在回来的路上，我实际上足够清醒地浏览了一下[的一个快速教程](http://www.somethinghitme.com/2013/01/09/creating-a-canvas-platformer-tutorial-part-one/)——如何使用 HTML5 Canvas API 在屏幕上绘图，接收输入并输出一个简单的单屏幕平台游戏。没有什么值得大书特书的，但是离开像 [Pixi](http://www.pixijs.com/) 和 [CreateJS](http://createjs.com/) 这样的图书馆，直接动手做点什么还是不错的。

## 新的挑战

教程(上面链接的)竟然是 2013 年的。翻阅一下，驱动这个简单应用程序所需的 javascript 相当简单，这个教程与其说是一堂 Javascript 课，不如说是一堂简单游戏编程课:玩家变量的概念、在内存中建模循环然后画出它，以及碰撞(基于盒子，但仍然)都包括在内。作为在 Unity 中有一些构建类似系统经验的人，这并不是什么特别新的东西。但是 Unity 的知识会驱使我更新本教程中的代码，这对于 Unity 使用 C#和除 Javascript 之外的几乎所有其他语言都非常有用，直到最近。

## ES6 类

教程将所有游戏状态保存在全局`window`对象中。其他各种对象(比如一个`player`对象，或者`boxes`数组)是顶级的，而像处理输入这样的项目是根据需要直接添加的。作为一个一直对保持一个干净的语义代码库感兴趣的人，我花了 3.5 个小时的时间(我们很幸运地买到了 Acela 的票)试图重构原作者所做的事情。

#### 代码

跟着 Github 上的代码[走。我不会在这里重复每一行，所以去检查整个事情了！(链接指向这篇文章引用的特定提交，因为我将在未来更新它)。](https://github.com/washingtonsteven/js-canvas-platformer/tree/95927ec3ff837ff3128472370b3b9f7b48464341)

### 主类

我从一个主类开始，这真的只是开始了一切。我有一个做某事的习惯

```
 $(document).ready(function() {
      main.init();
    });
    var main = {
      init:function() {
        // actually do stuff here
      }
    }; 
```

Enter fullscreen mode Exit fullscreen mode

至少，它把事情排除在全球范围之外。但还远远不够。也...还在依赖 jQuery？[这就是 2010 年的](https://meta.stackoverflow.com/questions/335328/when-is-use-jquery-not-a-valid-answer-to-a-javascript-question)。现在我们可以摆脱那堆臃肿的 <sup>[1](#jquery-disclaimer)</sup> 并以一种相当容易阅读的方式设置我们的主要入门类:Javascript 的`public static void main(String [] args)`。

```
 class CVS { //CVS is short for canvas, not a convenience store/pharmacy
      constructor() {
        // actually do stuff here
      }  
    }

    (function() {
      let cvs = new CVS();
    })(); 
```

Enter fullscreen mode Exit fullscreen mode

### 浏览确认

Javascript 不擅长在文件间共享资源。至少在浏览器中，你要么需要将你的所有工作串联成一个文件(对于生产来说你应该这样做，但是在 dev 中是一个痛苦)，要么添加一堆`<script>`标签(对于那些真的不关心 HTTP 请求的人)。幸运的是，您可以在单独的文件中工作，大量的包(`gulp`、`browserify`和`webpack`，仅举几例)将为您完成所有的连接工作。更好的是，使用一些模块，你甚至可以声明你想在 Javascript 中使用的文件。你所需要的只是一个`require`，然后嘣，你就有了来自另一个文件的资源。

### 玩家类

例如，`Player`应该有自己的类，在那里它可以跟踪自己的变量(位置、速度和大小等等)。为了清楚起见，我们将把它分离到一个单独的文件中，以保持代码库有组织(并防止 scm 中的冲突)。幸运的是 ES6 给了我们一个默认的( *fingerguns* )方法来公开一个类

#### player.js

```
 class Player {
      constructor() {
        this.x = 0; this.y = 0; //etc., etc.
      }

      move(horizontal, vertical) {
        // given the horizontal and vertical input directions, move the player in this frame by updating this.x and this.y
      }

      draw(ctx) {
        // Given a canvas 2d context, draw the player at the current position (this.x, this.y). 
        // For simplicity's sake, this player is a square and we can use the canvas rect() function
      }
    }

    export default Player; 
```

Enter fullscreen mode Exit fullscreen mode

最后一行是神奇的 <sup>[2](#es6imports)</sup> 。我们现在可以导出类(它实际上是一个函数，因为`class`只是 JS 原型“类”定义的语法糖，而不是真正的“新”规范。

### 碰撞！(util.js)

不检查碰撞很难有游戏！`util.js`的结尾(它不是一个真正的类，只是一个有函数集合的对象)有一些检查两个对象之间冲突的基本数学。我不会去探究它的数学原理(上面链接的教程在这方面做得很好)。但只要两个物体都能描绘出一个矩形，也就是说，它们有一个`x`和`y`位置，以及`width`和`height`，它就能检测出两个物体是否发生了碰撞。

### 其他类

#### [T1】input . js](#inputjs)

拥有一个输入类(基本上是单例的，就像上面的 Player 一样)，对于将输入事件发送到适当的位置是很有用的。玩家不需要知道什么键被按下了，只需要知道我们需要去什么方向！所以我们从他们那里抽象出来。这甚至允许灵活地交换我们正在使用的输入类型:键盘、鼠标、Wiimote，甚至精神控制 <sup>[3](#mind-control)</sup> 都是可能的！

```
 class Input {
      constructor(docBody) { //pass in a reference to document.body
        this.keys = [];
        docBody.addEventListener('keydown', (e) => { this.keys[e.keyCode] = true });
        docBody.addEventListener('keyup', (e) => { this.keys[e.keyCode] = false });
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这是`Input`的肉。一个数组跟踪哪些键被按下。任何有权访问输入实例的人都可以检查`Input.keys`并查看在任何给定时刻按下了什么键。这个类中的任何其他函数(目前来说)都只是为了让检查更容易。

在我写这篇文章的时候，我意识到对它的一个更新是让 Input 成为一个真正的 singleton。现在你可以有这个类的多个版本，这可能会很混乱。在很大程度上，我认为你只需要一个。

#### platform.js 和 level.js(还有 coin.js！)

`Platform`基本上是一个带有`draw`功能的美化矩形。给它传递一个画布上下文，它会用自己的内部状态(`x`、`y`、`width`和`height`)在画布上绘制一个`rect`。硬币是一样的，除了它们是圆的，并且有一个`collected`状态，下次抽取时会变灰。

`Level`是平台的集合。如果你让一个关卡自己画，它只是把命令传递给它的平台(和硬币)阵列。

#### (其余部分)util.js

util 的其余部分有一些很好的助手函数来帮助我们度过困难时期(并保持事物干燥)

*   `normalize(num)`:有时候，我们并不关心一个变量的值，只关心它是小于零、零还是大于零。这里有一个简单的方法。(别告诉我，Javascript 中已经有一个`Math`函数可以做到这一点)
*   `clamp(num, min, max)`:在这里我们可以传入一个数字，如果这个数字小于`min`，或者小于`max`，我们就返回这个数字，而不是到处进行边界检查。此外，还有一些简单的检查，以便`min`和`max`可以以任何顺序通过，如果你只通过一个边界，它会假设这是`max`和`min`是负的`max`。
*   这是一种简单的检查存在性的方法，而不必每次都剔除“虚假”的值。
*   `objectHasAllProps(o, props)`(和`objectsHaveAllProps`):仅用于`collide`，它是一种类型不存在的语言中的类型检查器。增加了复数形式，因为。

### 第二轮，开始！

所以这是第一遍(至少是一个简短的概述)。使用我正在学习的这个新工具完成这项工作非常有趣！下一步是实现更复杂的级别，可能会脱离绘图 API，使用实际的图像，放弃所有这些，只使用像 Pixi.js 这样的画布库，因为我没有时间编写自己的库。

再次关注 Github 上的代码(在本文发布时)

* * *

像 [Babel](https://babeljs.io/) 这样的工具使得导出/导入变量变得怪异，因为 Babel < 6.0 抑制了一些技术上无效的代码的错误。[点击此处阅读更多内容。](https://medium.com/@kentcdodds/misunderstanding-es6-modules-upgrading-babel-tears-and-a-solution-ad2d5ab93ce0)

精神控制 API 来啦~2020？