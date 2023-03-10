# 优化的前端

> 原文：<https://dev.to/sanjsanj/optimising-the-front-end-for-thebrowser>

这篇文章的最新版本可以在这里找到[https://hacker noon . com/optimizing-the-front-end-for-the-browser-f2f 51a 29 c 572](https://hackernoon.com/optimising-the-front-end-for-the-browser-f2f51a29c572)

优化就是速度和满意度。

*   对于用户体验(UX)，我们希望我们的前端提供一个快速加载和高性能的网页。
*   对于开发人员体验(DX ),我们希望前端快速、简单，堪称典范。

如果你已经花了很多时间来提高你的网站的 [Google Pagespeed Insights](https://developers.google.com/speed/pagespeed/insights/) 分数，那么这将有望阐明它实际上意味着什么，以及我们必须优化我们前端的过多策略。

## 背景

最近，我的整个团队有机会花一些时间对我们的代码库进行升级，可能会使用 React。这让我开始思考我们应该如何构建我们的前端。我很快意识到浏览器将是我们方法中的一个重要因素，也是我们知识中的一个同样大的瓶颈。

## 接近

### 首先

我们无法控制浏览器或改变它的行为方式，但我们可以理解它是如何工作的，这样我们就可以优化我们提供的有效载荷。

幸运的是，浏览器行为的基础相当稳定，[被很好地记录了下来](https://developers.google.com/web/fundamentals/)，并且在很长一段时间内不太可能发生重大变化。

所以这至少给了我们一个努力的目标。

### 第二

另一方面，代码、堆栈、架构和模式是我们可以控制的。它们更加灵活，变化速度更快，为我们提供了更多的选择。

### 因此

我决定从外向内工作，弄清楚我们代码的最终结果应该是什么，然后形成关于编写代码的意见。在第一篇博客中，我们将集中讨论所有我们需要知道的关于浏览器的事情。

## 浏览器做什么

让我们积累一些知识。这里有一些我们希望浏览器运行的普通 HTML。

```
<!DOCTYPE html>
<html>
  <head>
    The "Click the button" page
    <meta charset="UTF-8">
    <link rel="stylesheet" href="styles.css" />
  </head>

  <body>
    <h1>
      Click the button.
    </h1>

    <button type="button">Click me</button>

    <script>
      var button = document.querySelector("button");
      button.style.fontWeight = "bold";
      button.addEventListener("click", function () {
        alert("Well done.");
      });
    </script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

### 浏览器如何呈现页面

当浏览器接收到我们的 HTML 时，它会对其进行解析，将其分解成它能理解的词汇，由于有了 [HTML5 DOM 规范](https://www.w3.org/TR/html5/dom.html)，这在所有浏览器中都保持一致。然后，它通过一系列步骤来构建和呈现页面。这是非常高层次的概述。

1 -使用 HTML 创建文档对象模型( **DOM** )。
2 -使用 CSS 创建 CSS 对象模型( **CSSOM** )。
3 -在 DOM 和 CSSOM 上执行**脚本**。
4 -结合 DOM 和 CSSOM，形成**渲染树**。
5 -使用渲染树来**布局**所有元素的大小和位置。
6 - **在所有像素点绘制**。

[![An overview of the document render process](img/926d8a6869e66ef221bfde623325c0c1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lxJnbrcs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sanjsanj/otfe-assets/blob/master/otfe-overview.png%3Fraw%3Dtrue)

## 第一步——HTML

浏览器开始从上到下读取标记，并通过将其分解为节点来创建 DOM。

[![The HTML DOM](img/c13228382ea9b93e892b543199a11a5e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--XlaIT3fl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sanjsanj/otfe-assets/blob/master/otfe-dom-nodes-1.png%3Fraw%3Dtrue)

### HTML 投放优化策略

*   顶部是样式，底部是脚本

虽然这条规则也有例外和细微差别，但总的想法是尽可能早地加载样式，尽可能晚地加载脚本。这样做的原因是脚本需要 HTML 和 CSS 在执行之前完成解析，因此我们将样式放在高处，这样在我们在底部编译和执行脚本之前，它们有足够的时间进行计算。

接下来，我们将研究如何在优化的同时进行调整。

*   缩小和压缩

这适用于我们交付的所有内容，包括 HTML、CSS、JavaScript、图像和其他资产。

缩小删除任何多余的字符，包括空白、注释、多余的分号等。

压缩，例如 GZip，通过为代码或资产分配一个指向原始实例的指针来替换代码或资产中重复的数据。大规模压缩下载文件的大小，转而依靠客户端来解压文件。

通过这两种方法，你可以将有效载荷减少 80%或 90%。例如[仅 bootstrap 一项就节省了 87%](https://css-tricks.com/the-difference-between-minification-and-gzipping/#article-header-id-2)。

*   易接近

虽然这不会让你的页面下载更快，但它会大大提高受损用户的满意度。一定要给大家提供！在元素上使用`aria`标签，在图像上提供`alt`文本和[所有其他好东西](http://www.clarissapeterson.com/2012/11/html5-accessibility/)。

使用像 [WAVE](http://wave.webaim.org/) 这样的工具来确定你可以在哪些地方提高可访问性。

## 第二步–CSS

当它找到任何样式相关的节点时，即外部、内部或内联样式，它停止**呈现**DOM，并使用这些节点创建 CSSOM。这也是为什么他们称 CSS **【渲染阻塞】**的原因。下面是不同风格类型的一些利弊[。](https://vineetgupta22.wordpress.com/2011/07/09/inline-vs-internal-vs-external-css/) 

```
// External stylesheet
<link rel="stylesheet" href="styles.css">

// Internal styles
<style>
  h1 {
    font-size: 18px;
  }
</style>

// Inline styles
<button style="background-color: blue;">Click me</button> 
```

Enter fullscreen mode Exit fullscreen mode

CSSOM 节点的创建就像 DOM 节点一样，稍后它们会被合并，但现在它们看起来是这样的。

[![CSS nodes](img/8525f587efd8d188e5823fe7db870f62.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g-PyW04t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sanjsanj/otfe-assets/blob/master/otfe-cssom-nodes-1.png%3Fraw%3Dtrue)

CSSOM 的构造阻碍了页面的呈现，所以我们希望在树中尽可能早地加载样式，使它们尽可能轻量级，并在有效的情况下延迟加载它们。

### CSS 交付优化策略

*   使用[媒体属性](https://developer.mozilla.org/en-US/docs/Web/CSS/@media)。

媒体属性指定了加载样式必须满足的条件，例如，是否有最大或最小分辨率？是为了屏幕阅读器吗？

台式机非常强大，但移动设备却不是，所以我们希望尽可能减轻它们的负载。我们可以先假设只提供移动样式，然后把桌面样式作为媒体的条件，虽然这不会阻止它下载，但会阻止它加载我们的页面并耗尽宝贵的资源。

```
// This css will download and block rendering of the page in all circumstances.
// media="all" is the default value and the same as not declaring any media attribute.
<link rel="stylesheet" href="mobile-styles.css" media="all">

// On mobile this css will download in the background and not disrupt the page load.
<link rel="stylesheet" href="desktop-styles.css" media="min-width: 590px">

// Media query in css that only computes for a print view.
<style>
  @media print {
    img {
      display: none;
    }
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

*   推迟 CSS 的加载

如果你有样式，可以等到第一次有意义的绘制之后再加载和计算，例如，出现在折叠下面的内容，或者在页面有响应之前不重要的内容。在附加样式之前，您可以使用脚本等待页面加载。

```
<html>
  <head>
    <link rel="stylesheet" href="main.css">
  </head>

  <body>
    <div class="main">
      Important above the fold content.
    </div>

    <div class="secondary">
      Below the fold content.
      Stuff you won't see until after page loads and you scroll down.
    </div>

    <script>
      window.onload = function () {
        // load secondary.css
      }
    </script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个关于如何做到这一点的例子、[和另一个](https://www.giftofspeed.com/defer-loading-css/)。

*   特异性较低

一个明显的缺点是，随着更多的元素链接在一起，物理上有更多的数据要传输，从而扩大了 CSS 文件，但在计算具有更高特异性的样式时，也有客户端计算消耗。

```
// More specific selectors == bad
.header .nav .menu .link a.navItem {
  font-size: 18px;
}

// Less specific selectors == good
a.navItem {
  font-size: 18px;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   仅交付您需要的东西

这听起来可能很傻或者很傲慢，但是如果你在前端工作过一段时间，你就会知道 CSS 的一个大问题是删除内容的不可预测性。从设计上来说，它被诅咒了要不停地生长。

为了尽可能地精简 CSS，可以使用像 [uncss](https://github.com/giakki/uncss) 包这样的工具，或者如果你想要一个网络替代品，那么就四处看看，有很多选择。

## 第三步- JavaScript

然后，浏览器继续构建 DOM/CSSOM 节点，直到找到任何 JavaScript 节点，即外部或内联脚本。

```
// An external script
<script src="app.js"></script>

// An internal script
<script>
  alert("Oh, hello");
</script> 
```

Enter fullscreen mode Exit fullscreen mode

因为我们的脚本可能需要访问或操作先前的 HTML 或样式，所以我们必须等待它们全部构建完成。

因此，浏览器必须停止**解析**节点，完成构建 CSSOM，执行脚本，然后继续。这就是为什么他们称 JavaScript 为**“解析器阻塞”**。

浏览器有一种叫做“预加载扫描器”的东西，它会扫描 DOM 中的脚本并开始预加载它们，但是脚本只有在前面的 CSS 节点被构造之后才会按顺序执行。

如果这是我们的脚本:

```
var button = document.querySelector("button");
button.style.fontWeight = "bold";
button.addEventListener("click", function () {
  alert("Well done.");
}); 
```

Enter fullscreen mode Exit fullscreen mode

那么这将是对我们的 DOM 和 CSSOM 的影响。

[![Combined DOM and CSSOM nodes](img/f3c04ac3cf004d5a1a9efdc5f4de4652.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--HT_2dyFk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sanjsanj/otfe-assets/blob/master/otfe-combined-nodes-1.png%3Fraw%3Dtrue)

### JavaScript 交付优化策略

优化我们的脚本是我们能做的最重要的事情之一，也是大多数网站做得最差的事情之一。

*   异步加载脚本

通过在我们的脚本中使用一个`async`属性，我们可以告诉浏览器继续用另一个低优先级的线程下载它，但是不要阻塞其余的页面加载。一旦下载完成，它就会执行。

```
<script src="async-script.js" async></script> 
```

Enter fullscreen mode Exit fullscreen mode

这意味着它可以在任何时候执行，这导致了两个明显的问题。首先，它可能会在页面加载后很久才执行，所以如果我们依赖它为 UX 做一些事情，那么我们可能会给用户一个次优的体验。第二，如果它碰巧在页面完成加载之前执行，我们不能预测它将访问正确的 DOM/CSSOM 元素，并且可能会中断。

[![async script diagram](img/53ef3946bf8d151792d57fb011d191f5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--GtIpzMxT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sanjsanj/otfe-assets/blob/master/otfe-async-script.png%3Fraw%3Dtrue)

对于不影响我们的 DOM 或 CSSOM 的脚本来说是很棒的，对于不需要我们的代码知识并且对于 UX 来说不是必要的外部脚本来说肯定是很棒的，比如分析或跟踪。但是如果你发现它的任何好的用例，那么就使用它。

*   延迟加载脚本

`defer`与`async`非常相似，因为它不会阻止页面的加载，但是，它会等到我们的 HTML 被解析后再执行，并且会按照出现的顺序执行。

[![defer script diagram](img/1abad2c4193733f1e4d12824bfd0ef7e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rS-yzz2k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sanjsanj/otfe-assets/blob/master/otfe-defer-script.png%3Fraw%3Dtrue)

这对于脚本来说是一个非常好的选择，这些脚本将作用于我们的渲染树，但对于加载页面的折叠内容来说并不重要，或者需要之前已经运行的脚本。

```
<script src="defer-script.js" defer></script> 
```

Enter fullscreen mode Exit fullscreen mode

这里有[另一个使用延迟策略的很好的选择](https://varvy.com/pagespeed/defer-loading-javascript.html)，或者你可以使用类似`addEventListener`的东西。如果你想知道更多，那么[这里是](http://stackoverflow.com/questions/588040/window-onload-vs-document-onload)开始阅读的好地方。

```
// All of the objects are in the DOM, and all the images, scripts, links and sub-frames have finished loading.
window.onload = function () {
};

// Called when the DOM is ready which can be prior to images and other external content.
document.onload = function () {
};

// The JQuery way
$(document).ready(function () {
}); 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是,`async`和`defer`不能处理内联脚本，因为默认情况下，浏览器一得到它们就会编译并执行它们。当它们在 HTML 中内联时，它们会立即运行，通过在外部资源上使用上述两个属性，我们只是抽象掉或延迟了我们的脚本到我们的 DOM/CSSOM 的发布。

*   操作前克隆节点

当且仅当您在对 DOM 执行多次更改时看到不必要的行为时，请尝试这样做。

首先克隆整个 DOM 节点，对克隆节点进行修改，然后用它替换原来的节点，这样可以避免多次重绘，降低 CPU 和内存负载，这样可能会更有效。它还可以防止页面的“抖动”变化和无样式内容的闪烁( [FOUC](https://en.wikipedia.org/wiki/Flash_of_unstyled_content) )。

```
// Efficiently manipulating a node by cloning it

var element = document.querySelector(".my-node");
var elementClone = element.cloneNode(true); // (true) clones child nodes too, (false) doesn't

elementClone.textContent = "I've been manipulated...";
elementClone.children[0].textContent = "...efficiently!";
elementClone.style.backgroundColor = "green";

element.parentNode.replaceChild(elementClone, element); 
```

Enter fullscreen mode Exit fullscreen mode

克隆时要小心，因为它不会克隆事件侦听器。有时候这可能正是你想要的。过去，当事件监听器没有调用命名函数时，我们使用这种方法来重置事件监听器，并且我们没有 JQuery 的`.on()`和`.off()`方法可用。

*   预加载/预取/预呈现/预连接

这些属性基本上做到了他们在罐头上所说的，而且非常好。但是它们很新，没有普遍的浏览器支持，这意味着它们对我们大多数人来说不是真正的竞争者。但是如果你有那份闲情逸致，那就看看这里的和这里的。

## 第四步-渲染树

一旦读取了所有节点，并且准备好组合 DOM 和 CSSOM，浏览器就构建呈现树。如果我们把节点看作单词，把对象模型看作句子，那么渲染树就是一整页。现在，浏览器拥有了呈现页面所需的一切。

[![render tree diagram](img/683a7320b95ffb81fb5d8459b8916291.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WqGFz6OZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sanjsanj/otfe-assets/blob/master/otfe-render-tree.png%3Fraw%3Dtrue)

## 步骤五-布局

然后我们进入布局阶段，确定页面上所有元素的大小和位置。

[![layout diagram](img/f26c2130f39996774c18fe1de6e9dba4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kDK-dA_5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sanjsanj/otfe-assets/blob/master/otfe-layout.png%3Fraw%3Dtrue)

## 第六步——绘画

最后，我们进入绘画阶段，实际上将屏幕上的像素光栅化，为用户“绘画”页面。

[![paint diagram](img/b817b56c6aff5dd00a022d79cbbd7019.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--lOBkabZx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sanjsanj/otfe-assets/blob/master/otfe-paint.png%3Fraw%3Dtrue)

而这一切通常发生在几秒或几十分之一秒内。我们的工作是加快速度。

如果 JavaScript 事件改变了页面的任何部分，就会导致渲染树的重绘，并迫使我们重新进行布局和绘制。现代浏览器足够智能，只进行部分重绘，但我们不能指望这是有效的或高性能的。

话虽如此，显然 JavaScript 在很大程度上是基于客户端事件的，我们希望它操纵我们的 DOM，所以它将会这样做。我们只需要限制它的不良影响。

到现在为止，你已经知道了足够多的东西来欣赏程昕婷·加西尔的演讲。这是 2012 年的数据，但信息仍然正确。她关于这个主题的综合论文可以在这里阅读。

如果你喜欢到目前为止所读的内容，但仍然渴望了解更多的低级技术内容，那么你所有知识的先知就是 HTML5 规范。

我们就快到了，再陪我一会儿吧！现在我们发现了为什么我们需要知道以上所有的事情。

## 浏览器如何进行网络呼叫

在本节中，我们将了解如何最有效地向浏览器传输呈现页面所需的数据。

当浏览器请求一个 URL 时，我们的服务器用一些 HTML 来响应。我们将从非常小的开始，慢慢增加复杂性。

假设这是我们页面的 HTML。

```
<!DOCTYPE html>
<html>
  <head>
    The "Click the button" page
  </head>

  <body>
    <h1>
      Button under construction...
    </h1>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

我们需要学习一个新术语，关键渲染路径(CRP)。它只是意味着浏览器呈现页面需要的步骤数量。这是我们的 CRP 图表现在的样子。

[![CRP diagram 1](img/efb38983b6f4e11024347e6d26684cda.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1C1iRyML--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sanjsanj/otfe-assets/blob/master/otfe-crp-1.png%3Fraw%3Dtrue)

浏览器发出一个 GET 请求，它一直闲置，直到我们用页面所需的 1kb HTML(还没有 CSS 或 JavaScript)作出响应，然后它能够构建 DOM 并呈现页面。

### 关键路径长度

三个 CRP 指标中的第一个是路径长度。我们希望它尽可能的低。

浏览器与服务器进行一次往返，以检索呈现页面所需的 HTML，这就是它所需要的全部。因此我们的关键路径长度是 1，完美。

现在让我们把它提升一个档次，加入一些内部风格和 JavaScript。

```
<!DOCTYPE html>
<html>
  <head>
    The "Click the button" page
    <style>
      button {
        color: white;
        background-color: blue;
      }
    </style>
  </head>

  <body>
    <button type="button">Click me</button>

    <script>
      var button = document.querySelector("button");
      button.addEventListener("click", function () {
        alert("Well done.");
      });
    </script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

如果我们检查我们的 CRP 图表，我们应该看到一些变化。

[![CRP diagram 2](img/5194f106406c1f9bd8b6249b90366967.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---a2Sa2LS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sanjsanj/otfe-assets/blob/master/otfe-crp-2.png%3Fraw%3Dtrue)

我们有两个额外的步骤，**构建 CSSOM** 和**执行脚本**。这是因为我们的 HTML 有需要计算的内部样式和脚本。然而，因为不需要发出外部请求，所以它们不会增加我们的关键路径长度，耶！

但是等等，不要这么快。还要注意，我们的 HTML 大小增加到了 2kb，所以我们不得不在某个地方做些改动。

### 临界字节

这就是我们三个指标中的第二个指标的用武之地，关键字节。这度量了呈现页面需要传输多少字节。并不是所有的字节都将下载到页面上，而是真正呈现我们的页面并使其响应用户所需的字节。

不用说，我们也想尽量减少这种情况。

如果你认为这很好，谁还需要外部资源，那你就错了。虽然这看起来很诱人，但在大规模上并不可行。实际上，如果我的团队必须交付一个包含内部或内联所需的所有内容的页面，这将是一个巨大的工程。并且浏览器不是为处理这样的负载而构建的。

看看[这篇有趣的文章](https://www.ctheu.com/2015/08/17/react-inline-styles-vs-css-stupid-benchmark/)关于像 React 推荐的那样内联所有样式时页面加载的影响。DOM 的大小增加了四倍，装载时间增加了一倍，响应时间增加了 50%。相当不能接受。

还要考虑这样一个事实，即外部资源可以被缓存，因此当再次访问我们的页面，或者访问使用相同资源的其他页面(例如`my-global.css`)时，浏览器将不会进行网络调用，而是使用其缓存版本，从而对我们来说是一个更大的胜利。

因此，让我们成长起来，并使用外部资源为我们的风格和脚本。注意，我们有 1 个外部 CSS 文件、1 个外部 JavaScript 文件和 1 个外部异步 JavaScript 文件。

```
<!DOCTYPE html>
<html>
  <head>
    The "Click the button" page
    <link rel="stylesheet" href="styles.css" media="all">
    <script type="text/javascript" src="analytics.js" async></script>  // async
  </head>

  <body>
    <button type="button">Click me</button>

    <script type="text/javascript" src="app.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的 CRP 图表现在的样子。

[![CRP diagram 3](img/a34ab2afce5cd995b4a643f844f73b93.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VZg0EnaK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sanjsanj/otfe-assets/blob/master/otfe-crp-3.png%3Fraw%3Dtrue)

浏览器得到我们的页面，构建 DOM，一旦它发现任何外部资源，预加载扫描器就会启动。这将继续下去，并开始下载它可以在 HTML 中找到的所有外部资源。CSS 和 JavaScript 优先级较高，其他资源优先级较低。

它捡起了我们的`styles.css`和`app.js`,并开辟了另一条关键路径去取它们。它没有选择我们的`analytics.js`，因为我们给了它`async`属性。浏览器仍然会用另一个低优先级的线程下载它，但是因为它不会阻止我们页面的渲染，所以它不会影响到关键路径。这正是谷歌自己的优化算法对网站进行排名的方式。

### 关键文件

最后，我们的最后一个 CRP 指标，关键文件。浏览器为呈现页面而必须下载的文件总数。在我们的案例 3 中，HTML 文件本身、CSS 和 JavaScript。`async`剧本不算。当然，越小越好。

### 回到关键路径长度

现在你可能会想，这肯定是有史以来最长的关键路径了吧？我的意思是，我们只需要下载 HTML、CSS 和 JavaScript 来呈现我们的页面，我们已经在两次往返中完成了。

### HTTP1 文件限制

再说一次，生活没那么简单。由于 HTTP1 协议，我们的浏览器一次只能从一个域同时下载设定的最大数量的文件。它的范围从非常老的浏览器的 2 到 Edge 的 10，Chrome 处理 6。

您可以在此查看您的用户浏览器可以从您的域[请求的最大并发文件数。](http://sgdev-blog.blogspot.co.uk/2014/01/maximum-concurrent-connection-to-same.html)

你可以也应该通过提供一些影子域之外的资源来最大化你的优化潜力。

**警告:**不要从你的根域之外的任何地方提供关键的 CSS，DNS 查找和延迟本身将在任何可能的情况下抵消任何可能的好处。

### HTTP2

如果你的网站是 HTTP2 并且你的用户的浏览器是兼容的，那么你可以完全取消这个限制。但是这些美好的事情还不太常见。

你可以在这里测试你的网站的 HTTP2'ness。

### TCP 往返行程限制

另一个敌人靠近了！

任何一次往返中可以传输的最大数据量是 14kb，这适用于所有 web 请求，包括 HTML、CSS 和脚本。这来自于 [TCP 规范](https://hpbn.co/building-blocks-of-tcp/#slow-start)防止网络拥塞和数据包丢失。

如果我们的 HTML 或请求中的任何资源累积大于 14kb，那么我们需要进行额外的往返来获取它们。所以，是的，这些巨大的资源确实为我们的 CRP 增加了许多途径。

## 大哈纳

现在让我们全力以赴制作我们庞大的网页。

```
<!DOCTYPE html>
<html>
  <head>
    The "Click the button" page
    <link rel="stylesheet" href="styles.css">     // 14kb
    <link rel="stylesheet" href="main.css">       // 2kb
    <link rel="stylesheet" href="secondary.css">  // 2kb
    <link rel="stylesheet" href="framework.css">  // 2kb
    <script type="text/javascript" src="app.js"></script>  // 2kb
  </head>

  <body>
    <button type="button">Click me</button>

    <script type="text/javascript" src="modules.js"></script>    // 2kb
    <script type="text/javascript" src="analytics.js"></script>  // 2kb
    <script type="text/javascript" src="modernizr.js"></script>  // 2kb
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

我知道一个按钮需要很多 CSS 和 JavaScript，但它是一个非常重要的按钮，对我们来说意义重大。所以我们不要妄加评论，好吗？

我们的整个 HTML 被很好地缩小和压缩，大小为 2kb，远低于 14kb 的限制，所以它在 CRP 的一次往返中返回给我们，浏览器尽职尽责地开始用 1 个关键文件，即我们的 HTML，构建我们的 DOM。

[![CRP diagmra 4-1](img/cde1ad2e08964e5d8fc8e0f30d8b9238.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xHVhPg-m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sanjsanj/otfe-assets/blob/master/otfe-crp-4-1.png%3Fraw%3Dtrue)

`CRP Metrics: Length 1, Files 1, Bytes 2kb`

它获取一个 CSS 文件，并通过预加载扫描器识别所有外部资源(CSS 和 JavaScript ),并请求开始下载它们。但是等一下，第一个 CSS 文件是 14kb，最大化了一次往返的有效载荷，所以它本身就是一个 CRP。

[![CRP diagram 4-2](img/a7a3e616b18b95550136463331339ef7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Pw3igpL3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sanjsanj/otfe-assets/blob/master/otfe-crp-4-2.png%3Fraw%3Dtrue)

`CRP Metrics: Length 2, Files 2, Bytes 16kb`

然后它继续下载资源。其余的重量在 14kb 以下，所以可以在一次往返中完成，但有 7 个，因为我们的网站还没有在 HTTP2 上，我们使用的是 Chrome，所以在这次往返中我们只能下载 6 个文件。

[![CRP diagram 4-3](img/e6aa337a5f0d50ce90a5b47ee71456d7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s79gpfQI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sanjsanj/otfe-assets/blob/master/otfe-crp-4-3.png%3Fraw%3Dtrue)

`CRP Metrics: Length 3, Files 8, Bytes 28kb`

现在我们终于可以下载最终文件并呈现 DOM 了。

[![CRP diagram 4-4](img/d5ecc4c337393d6462ef548c0caf5f2c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KVgyf407--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/sanjsanj/otfe-assets/blob/master/otfe-crp-4-4.png%3Fraw%3Dtrue)

`CRP Metrics: Length 4, Files 9, Bytes 30kb`

使我们的 CRP 达到 9 个关键文件和 4 个关键路径中总共 30kb 的关键资源。有了这些信息和一些关于连接延迟的知识，您实际上可以开始对给定用户的页面性能做出真正准确的估计。

## 浏览器联网优化策略

*   Pagespeed 洞察

使用[洞察力](https://developers.google.com/speed/pagespeed/insights/)识别绩效问题。Chrome DevTools 中还有一个`audit`标签。

*   精通 Chrome 开发者工具

开发工具太神奇了。我可以单独写一整本书，但是已经有很多资源可以帮助你了。这里有一本真正有用的读物，可以用来开始解读网络资源。

*   在良好的环境中构建，在恶劣的环境中测试

当然，你希望在 Macbook Pro 上安装 1tb SSD 和 32gb RAM，但对于性能测试，你应该前往 Chrome 中的`network`标签，如果你使用的是 Chrome，并模拟低带宽、节流 CPU 连接，以真正获得一些有用的信息。

*   将资源/文件连接在一起

所以在上面的 CRP 图表中，我省略了一些你当时不需要知道的东西。但基本上，在收到每个外部 CSS 和 JavaScript 文件后，浏览器会构建 CSSOM 并在其上执行脚本。因此，即使您可能在一次往返行程中提交几个文件，它们每个都会使浏览器浪费宝贵的时间和资源，所以最好在可行的情况下将文件组合在一起，并消除不必要的负载。

*   页眉中折叠内容上方的内部样式

对于是否内化或内联 CSS 和 JavaScript 以停止获取外部资源，并没有真正的黑白之分，相反，将资源外化以便可以缓存它们以保持 DOM 轻量级。

但是有一个很好的理由来支持在折叠内容之上使用内部样式，这意味着你可以避免获取资源来进行第一次有意义的绘制。

*   缩小/缩小图像

非常简单和基本，有很多选择，选择你最喜欢的。

*   将图像加载延迟到页面加载之后

使用一些简单的普通 JavaScript，您可以推迟加载出现在文件夹下的图像或者对于第一个用户响应状态不重要的图像。这里有一个很好的策略来做这件事。

*   异步加载字体

加载字体是非常昂贵的，如果可以的话，你应该使用带有后备的网络字体，然后逐步渲染你的字体和图标。这可能看起来不太好，但是另一个选择是，如果字体没有加载，你的页面加载时没有任何文本，这被称为不可见文本的闪现。

*   你真的需要那些 JavaScript/CSS 吗？

你知道吗？回答我！有没有一个本地的 HTML 元素可以产生你使用脚本的行为？有没有可以内联而不是内部/外部创建的样式或图标？例如[内联一个 SVG](https://css-tricks.com/using-svg/#article-header-id-7) 。

*   CDNs

内容交付网络(cdn)可用于从物理距离更近、延迟更低的位置向用户提供资产服务，从而降低加载时间。

### 写得好

现在你高兴极了，知道了足够多的东西，可以去那里自己发现更多关于这个主题的东西。我会推荐参加这个免费的 Udacity 课程并阅读谷歌自己的关于优化的 T2 文档。

如果你渴望更低级的知识，那么这本关于[高性能浏览器网络](https://hpbn.co/)的免费电子书是一个很好的起点。

## 包装完毕

关键的渲染路径是非常重要的，它给了我们一些非常可靠和合理的规则来优化我们的网站。重要的 3 个指标是:

1-关键字节数。
2-关键文件的数量。
3-关键路径的数量。

我在这里写的东西应该足以让你掌握基本原理，并帮助你解释 Google Pagespeed Insights 对你的表现的看法。

最佳实践的应用将与良好的 DOM 结构、网络优化和我们可用于最小化 CRP 指标的各种策略相结合。让用户更开心，让谷歌的搜索引擎更开心。

在任何企业规模的网站，这将是一个巨大的任务，但你迟早要做，所以停止招致更多的技术债务，开始投资于坚实的性能优化策略。

非常感谢你读到这里，如果你真的做到了。我真的希望这对你有所帮助，如果你有任何反馈或对我的更正，请发送给我。

在这篇博客的下一部分，我希望给出一个真实的例子，说明我将如何在我自己团队的庞大代码库中实现所有这些原则。我们在一个域上拥有超过 2000 个内容可管理的页面，每天有成千上万的页面浏览量，所以这会很有趣。

但那可能还需要一段时间，我现在需要躺下来。