# Javascript 的数字很奇怪，但是如果你理解它们，你就可以接受它们

> 原文：<https://dev.to/jestingrabbit/javascripts-numbers-are-weird-but-you-can-live-with-them-if-you-understand-them-56pb>

最近，一个朋友遇到了一个非常令人困惑的问题。他们将 id 作为 javascript 数字存储在客户机中，并开始发现后端发送的内容和解析 AJAX 响应后看到的内容之间的差异。这不是一个巨大的差异，但是任何差异都会引起问题(正如您所料)。

发生了什么事？

要解决这个问题，你需要理解 javascript 是如何表示数字的。

## Javascript 的数字有点诡异

您可以在浏览器的控制台中进行以下计算。

```
0.1;
// 0.1
0.1 + 0.1; 
// 0.2
0.1 + 0.2;
// 0.30000000000000004 
```

一些人对这个愚蠢的错误一笑置之，指出计算机仍然是垃圾。我甚至听到一个家伙开玩笑说，这个错误就是几年前马特·达蒙被困在火星上的原因。

现在，乐趣是乐趣，但是让我们探索。

```
var martianError = function(){
  var epsilon = 1;
  while(((1-epsilon) !== 1) && ((1+epsilon) !== 1)){
    epsilon *= 0.5;
  }
  epsilon *= 2; // little bit more error for the hell of it (more or less).

  var maxDistanceFromEarthToMars = 410000000000;
  var distanceError = epsilon * maxDistanceFromEarthToMars;

  console.log("If you represent the distance to mars with "
            + "IEEE754 on this machine, you are out by "
            + distanceError + " metres, which is "
            + distanceError*1000+ " millimetres.");

  var thinPaperThickness = 70*0.000001;
  var thickPaperThickness = 180*0.000001;

  if(distanceError > thickPaperThickness){
    console.log("This is more than the thickness of thick paper.");
  } else if(distanceError < thinPaperThickness){
    console.log("This is less than the thickness of thin paper.");
  } else {
    console.log("This is about the thickness of a piece of paper.")
  }

  var averageDiameterOfHumanHair = 0.000100;
  var errorInHairWidths = distanceError / averageDiameterOfHumanHair;

  if (errorInHairWidths < 1) {
    console.log("Its less than the average diameter of a human hair.")
  } else {
    console.log("Its about " + errorInHairWidths.toFixed(0) + " human hair diameters.");
  }
}

martianError(); 
```

如果您将它复制并粘贴到 js 控制台中(并且您使用的是最新的 chrome 客户端)，您会发现当我们用 javascript 表示数量时，误差是多么小。

所以，有时候 javascript 中的数字会做一些愚蠢的事情，同时它们又非常精确。这两者怎么可能都是真的呢？

就像你使用的每一个工具一样，要想用好它，尝试找出它的设计目的是解决什么问题，它的设计的优点和缺点，并且清楚地记住你想用它做什么是一个好主意。

让我们从理解什么是 javascript 数字开始。

## Javascript 的数字是 IEEE 754 浮点数

每当 javascript 表示一个数字时，计算机就使用一种称为 IEEE 754 的数字格式。

IEEE 是电气和电子工程师协会，其目标是电气和电子工程、电信、计算机工程和相关学科的教育和技术进步。它有一个标准协会，他们定期[创建标准。](https://en.wikipedia.org/wiki/IEEE_Standards_Association#Notable_IEEE_Standards_committees_and_formats)它们主要描述了现代计算硬件的关键方面，以及现代浮点数的工作方式在 80 年代的 IEEE 754 中被标准化。

浮点数是计算机试图描述的量，如或 2 的平方根，以及事物的测量值，如到火星的距离和一根头发的宽度，以及使用这些量的计算结果。他们用一种复杂的方式来做这件事，使用一个符号位，一个基数，一个基数的指数，以及一些数字，或者更确切地说，一些被称为尾数的位。一起使用，这些描述星等的方式与[科学符号](https://www.chem.tamu.edu/class/fyp/mathrev/mr-scnot.html)描述星等的方式非常相似。在您现在的计算机上，您可能有 11 位用于描述指数，52 位用于描述尾数(这是表示精度的来源)。

0.1 的表示看起来像

```
0 01111111011 1001100110011001100110011001100110011001100110011010 
```

第一位表示数字是正数，接下来的 11 位表示它在十六分之一和八分之一之间，其余的位使用二进制表示它在这两个 2 的幂之间的确切位置(实际上有一个隐藏的额外位，使 53 成为一个神奇的数字，舍入以一种聪明的方式完成，但这些是技术上的)。您可以尝试如何在这里表示其他数字。

[https://codepen.io/jestingrabbit/embed/Xqvxmv?height=600&default-tab=result&embed-version=2](https://codepen.io/jestingrabbit/embed/Xqvxmv?height=600&default-tab=result&embed-version=2)

我对浮点数的表示如此自信的最大原因是因为 IEEE 754 标准非常成功。当它出现时，很多计算都是关于复杂的物理和经济系统的科学建模。有大量的大型计算机被设计和销售，它们有不同的方式来表示浮点数，新的和不同的浮点结构经常被设计出来。在这些设计中，有时做出了好的选择，有时又做出了坏的选择。

数学库必须针对每一个新的架构进行检查，并且经常重写，并且没有任何保证你和我的计算最终会得到相似的结论(特别是当计算处理一种叫做下溢的东西时)。

IEEE 754 做出了很好的体系结构选择，因此新的大型机体系结构的设计者可以担心标准的实现，而不是开发新的浮点系统。涉及复杂计算的代码可以在不同系统的研究人员之间共享，确信该架构将通过浮点计算做正确的事情。

IEEE 754 大大提高了计算效率。然后我们用电脑做的事情改变了。我们不再把它们用于科学计算，而开始把它们用于金钱、社交媒体和网络游戏，除此之外可能还有许多其他东西。IEEE 754 不是为这些而构建的，但对于数字系统来说，它仍然是一个很好的选择，因为它擅长 3D 建模，并且可以用一个系统表示整数和分数。

但也有怪癖。

## 浮点数不太适合金钱、网络点数或数据库 id

还记得我那个有奇怪的数据库 ID 问题的朋友吗？他们意识到 id 开始变得不可靠的值是高 16 位数。64 位浮点数不能精确表示的最小正整数是 2 <sup>53</sup> + 1，在十进制中是 16 位长，以 9 开头。我朋友的值被截断，以适应他们的 IEEE 754 表示的 52 位尾数。因此我们可以得出结论，javascript 的数字与大多数数据库表中用作 id 的 64 位整数不兼容。

这个故事的寓意:**永远不要将数据库 id 作为数字存储在客户机中。**将它们作为代表数字的十进制字符串传递给客户端。在运行的第一年，它可能不会咬你，但是在你为这样一个非常棘手的问题挠头之前，做一点明智的规划会更容易。另外，请注意，您永远也不会想要使用 ID 进行计算，因此将它存储为一个数字是没有必要的。

钱呢？10 美分加 20 美分正好是 30 美分，而不是大约 30 美分，人们对涉及金钱的计算和数字相当挑剔。我认为人们在购买时在服务器上等待一段时间是可以接受的，所以我的建议是在后端进行货币计算，使用类似于 [ruby 的 BigDecimal，](http://ruby-doc.org/stdlib-2.5.1/libdoc/bigdecimal/rdoc/BigDecimal.html)这样的数字类型，并将它们作为字符串传递给前端。其他方法也可以工作，比如处理感兴趣的最小值的整数表示(也许相信你的数字会很小)。但是，如果这个最小值发生变化(例如，企业突然想要跟踪十分之一美分)，您将不得不重写一些非常重要的业务逻辑。另一方面，使用专用的小数库是未来的保证。

对于游戏分数，或者一条推文的点赞数，或者自你上次在时间轴上发布以来的秒数，你需要考虑可以容忍多少错误，这些价值对人们有多重要，以及这些价值可能会有多大(一条推文可能有一天会获得超过 2 <sup>53</sup> 个赞，但这不会是明天)。既然知道了 javascript 数字的局限性，权衡这些就容易多了。

## IEEE 754 其实挺酷的

如果你对 20 世纪 80 年代在大型主机上从事大量科学建模任务的人说，人们将在适合他们口袋的计算机上进行大量商业活动，他们可能至少会有点惊讶。

如果你继续描述社交媒体追踪的价值、MMO 点数以及我们现代网络应用追踪的所有数字，他们可能会更惊讶，他们可能会说“浮点数可能不太适合这些目的”。如果您提到使用它们来跟踪数据库 id，他们会对整数和浮点数中有多少位有疑问，并很快得出结论，您正在走向一个问题。

现在我们的生活稍微简单了一些。绝大多数整数是 64 位整数(有时也称为`long`)，我们的浮点数都是 IEEE 754，通常是 64 位(有时称为`double`)。但是，如果一种语言没有为整数提供单独的数值类型，比如 javascript，那么当你试图用这种语言表示大整数时，就会变得有些棘手。

浮点数最擅长的一件事是 3D 图形，这使得像 [`three.js`](https://threejs.org/) 和增强现实应用程序这样的事情成为可能，这非常酷。IEEE 754 使得所有的天气模型和各种各样的其他东西成为可能，让人类在重要的方面进步，这也是超级酷的(以一种不太时髦的方式)。

对于他们被要求解决的问题，IEEE 数字是伟大的。只有当我们试图将它们扩展到用例之外时，事情才会分崩离析。我们可以避免这种情况，当我们需要时依靠服务器，当我们不需要时不使用数字，当人们不需要十几位精度时给他们有意义的截断值。