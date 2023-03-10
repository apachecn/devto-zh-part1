# JavaScript 函数简介

> 原文：<https://dev.to/simplymichael/introduction-to-javascript-functions-dbe>

## 简介

从作为 web 开发的前端脚本语言开始，Javascript 已经得到了发展，现在正被部署在多个堆栈和应用程序环境中，其中值得注意的是它在 Node.JS 的后端开发中的使用。因此，越来越多的开发人员和团队正在利用它来构建从最简单到最复杂的各种类型的应用程序。

因此，精通 JavaScript 被认为是(web)开发人员最重要的技能之一。据《计算机周刊》报道，软件分析公司 Cast 最近的一项调查发现，更大比例的开发人员(56%)认为 JavaScript(与 Java 一起)是“未来五年需要掌握的最重要的语言”。相比之下，C++ (38%)，Python (35%)和 SQL (30%)。

JavaScript 最强大但有时被忽视的特性之一是它对函数的使用。JavaScript 主要是一种函数式语言，函数在 JavaScript 中占有重要地位。

## 什么是函数

简单地说，函数是一组可以被调用的代码。换句话说，函数是一种将一些代码组合在一起，给这个组一个名称，然后使用给定的名称调用代码的方法。

## 为什么函数

函数的使用为开发人员提供了几个好处。下面重点介绍其中一些。

*   **封装和代码重用** -函数通过封装重复的任务或例程来促进代码重用，潜在地节省您更多的打字时间。这也有助于避免潜在的(键入)错误，这种错误可能是由于每当您需要执行函数所执行的动作时，都必须键入函数所封装的代码组而引起的。
*   更好的代码组织 -函数有助于组织和管理代码。一旦您编写了函数并测试它是否工作，您就可以保存它并在需要时调用它。此外，如果您需要对代码进行更改，您只需要在一个地方进行，而不是寻找您键入代码的每个实例。
*   简洁和自我文档化的代码——它们使我们的代码更短，并允许更干净的语法。恰当命名的函数使代码更加自文档化。

## 如何定义和使用函数

假设您有一系列代码语句来计算两个数的和，并将结果打印到浏览器，您可以这样做:

```
 var a = 5;
    var b = 7;
    var sum = a + b;
    document.write(sum); 
```

Enter fullscreen mode Exit fullscreen mode

然后，几行代码后，您更改存储在 a 和 b 变量中的一个或两个值，并再次设置 sum 等于 a 和 b 的相加，最后用 document.write(sum)打印 sum；。取决于您必须执行该组语句的次数，它可能会变成一场噩梦，尤其是当有问题的代码正在做一些非平凡的事情时。这是函数派上用场的一个好例子。让我们看看怎么做。

### 定义一个函数

要将上述代码转换为函数:我们遵循以下步骤:

- **一起收集我们的代码序列** :

```
var a = 5;
var b = 7;
var sum = a + b;
document.write(sum); 
```

Enter fullscreen mode Exit fullscreen mode

- **用花括号**将这组代码括起来，创建一个代码块:

```
{
   var a = 5;
   var b = 7;
   var sum = a + b;
   document.write(sum);
} 
```

Enter fullscreen mode Exit fullscreen mode

通过将我们的代码包含在左括号和右括号{}中，我们有效地将它们变成了一个执行单元(称为代码块)，既可以作为一个单元执行。
- **最后，给单元起一个名字**，名字前面是 function 关键字，后面是一组括号():

```
function sumAndPrint()
{
   var a = 5;
   var b = 7;
   var sum = a + b;
   document.write(sum);
} 
```

Enter fullscreen mode Exit fullscreen mode

到了最后一步，我们已经成功地定义了我们的函数，当我们需要求和并打印数字 5 和 7 的结果时，就可以调用它了。在左花括号和右花括号之间的代码块被称为函数体。

### 调用本方函数

因此，我们已经完成了函数的定义，将它封装在一个代码块中，并给它起了一个有趣的名字。但是我们如何使用这个功能呢？为了使用我们新定义的函数，我们简单地通过它的名字调用它，后跟一对括号(不包括函数关键字和函数体):

`sumAndPrint();`//打印 12 张

这就是在 JavaScript 中定义和使用函数是多么简单。调用函数的另一个术语是“调用”函数。

## 改进我们的功能

我们上面定义的函数是对每次我们需要执行两个数相加并输出结果时都必须手动键入它所包含的代码块的改进。尽管如此，它也有一些缺点。

*   首先，当前形式的函数只能对数字 5 和 7 求和，而不能对任何其他数字对求和。这意味着，如果我们需要对任何一组数字求和，我们将不得不为每一对新的数字编写不同的函数。这并不是特别理想的，因为这将打破枯燥(不要重复自己)的原则，并把我们带回到最初编写函数的原因:避免不必要的或可避免的重复。

*   另一个问题是，我们的函数——就像现在这样——一次做太多(实际上只有两件)事情:它计算给定数字的总和。
    打印计算出的总和。
    在创建函数时，一个很好的经验法则是，一个函数应该只做一件事；毕竟，函数应该帮助我们在代码中建立模块化。但是我们现在的功能打破了这个规则。

*   我们的函数的第三个问题是，在函数执行完毕后，我们目前没有办法得到这些数字的总和。这是因为在退出函数时，a、b 和 sum 变量超出了作用域，并且不能在函数外部访问。所以，如果我们需要在函数执行完毕后对计算出的值做些什么，我们不能。

让我们看看如何改进我们的功能，以处理我们提出的每个问题。

## 函数参数和返回值

第一个问题——只能对数字 5 和 7 求和，因此违反了 DRY 原则——可以通过所谓的函数参数来解决。函数参数是我们赋予一个函数使其能够执行任务的参数。

打个比方，想象让一个孩子去跑腿，这个跑腿可能是去帮 A 先生传递信息。在这种情况下，你不期望孩子给 A 先生任何有形的东西，只是传达一个(口头)信息。我们目前的职能就是如此。你不给它任何外部的东西来完成它的任务。相反，函数本身包含了完成工作所需的一切。

现在想象一下，让孩子去当地商店帮忙买一些东西。在这种情况下，你需要给孩子一些交换的手段，比如钱，这样孩子才能得到你想要的东西。否则，他们就没办法给你买到你想要的东西。在编程语言中，我们称这种行为的使能因素(在孩子出差的情况下，是钱)为“参数”。通过将参数包含在函数定义的左括号和右括号之间，可以将参数传递给函数。多个参数用逗号分隔。

```
function exampleFunction(param1, param2, param3, ...) 
{
   //function code goes here
} 
```

Enter fullscreen mode Exit fullscreen mode

回到我们之前的函数 sumAndPrint，它需要两个数来完成求和的任务——目前是 5 和 7，分别存储在变量 a 和 b 中。然而，我们希望这个函数能够对任意一对数字求和，而不仅仅是 5 和 7。
为了实现这一功能，我们需要:

*   将 a 和 b 变量的声明移出函数体。将它们作为参数传递给函数，在左括号和右括号之间。
*   当调用该函数时，向它传递实际的实参，实参将按照传递的顺序分配给形参。下面是我们的代码在应用这些更改后的样子:

```
function sumAndPrint(a, b)
{
   var sum = a + b;
   document.write(sum);
} 
```

Enter fullscreen mode Exit fullscreen mode

函数的参数列表中的 a 和 b 现在充当实际值的占位符，我们将在调用函数时传递这些值。通过对函数定义的这一简单更改，我们的函数可以接受传递给它的任意两个数字并对其求和。为了使用我们更新后的函数，我们像往常一样调用它，但是这一次，我们传递我们希望求和的数字作为参数:

`sumAndPrint(3, 6);` //9。

在内部，数字 3 将被分配给 a 变量，而数字 6 将被分配给 b 变量。这解决了第一个问题，现在让我们继续解决我们之前提出的第二个问题。

在这次更新中，我们想让我们的函数只做一件事。这可以通过将打印计算出的总和的代码移到函数之外来实现。然而，当我们这样做时，我们将不再能够打印出函数执行的计算结果，因为如前所述，没有办法在函数外部访问 sum 变量。这就把我们带到了上面提到的第三个问题:如何从函数的外部访问函数的结果，以便我们可以使用它，例如在另一个计算中使用它或者把它输出到浏览器，就像我们在这里做的那样。
在移出打印代码后，重命名我们的函数也是一个好主意，这样就可以清楚地看到它不再做两件事——求和与打印——而是做一件事(求和)。更新后的函数的一个好名字是“sum”。

为了访问函数的结果，从而解决上面提到的第三个问题，我们需要让函数给出它的结果值。
同样，使用出差儿童的类比，在从当地商店获得物品后，儿童必须将购买的物品“归还”给寄件人。以同样的方式，在执行了它的计算(或者它被编写来执行的任何任务)之后，可以指示一个函数将它的计算结果返回给调用者。这是通过“返回”语句来完成的。“return”语句由关键字 return 和我们希望函数返回给调用代码的值组成。

return 关键字基本上是对我们的函数说，“当你完成执行时，将这个值发送回你的调用者”。
应用这些更改——将打印到浏览器的代码移出，重命名我们的函数，并添加 return 语句——到我们的函数，它现在应该看起来像这样:

```
function sum(a, b)
{
   var sum = a + b;
   return sum;
} 
```

Enter fullscreen mode Exit fullscreen mode

其实我们完全可以去掉中间的 sum 变量，直接返回表达式 a + b 的结果就可以了:

```
function sum(a, b)
{
   return a + b;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将调用这个函数的结果存储在另一个变量中，并按照我们的意愿使用它:

```
var result = sum(5, 6);
document.write(result); 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到函数的使用如何使我们的代码更简洁、更清晰、更自文档化，这些特征是程序员非常需要的。函数名“sum”在我们的代码或客户端代码中的任何地方都是不言自明的。

## 关于 JavaScript 参数的更多内容

### 默认参数

JavaScript 规范的最新版本(ES6)在定义函数时支持可选参数的概念。简单地说，可选参数意味着在创建函数时，我们的函数参数被赋予默认值，这样，如果用户在调用函数时没有传递任何相应的参数，就使用默认值。例如，我们可以将求和函数定义如下:

```
function sum(a = 5, b = 7)
{
   return return a + b;
} 
```

Enter fullscreen mode Exit fullscreen mode

这将允许函数在传递时被调用:两个、一个或根本没有参数。
`sum(3, 6);`在这里，3 被赋给 a 变量，6 被赋给 b.
`sum(3);`在这个实例中，3 将被赋给 a 变量，由于我们没有提供第二个参数，默认值 7 将被赋给 b 变量，结果为 10。
`sum();`在这个调用中，我们没有传递任何参数，所以使用默认值 5 和 7，结果得到值 12。

### 处理早期版本的 JavaScript

ES6 之前的 JavaScript 版本不直接支持默认参数的概念；但是我们可以通过稍微修改我们的代码来实现相同的功能，同时利用 JavaScript 函数的一个特性:
JavaScript 允许我们在不传递任何参数的情况下调用函数，即使函数定义包含参数。

```
function sum(a, b)
{
   if(typeof a !== "number") {
      a = 5;
   }

   if(typeof b !== "number") {
      b = 7;
   }

   return a + b;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了什么事？我们使用 typeof 操作符来确定调用函数时传递给函数的参数的数据类型(或者不传递)。
对于每个参数，如果没有传递相应的实参，typeof 将为该参数返回字符串“undefined”。例如，如果我们调用函数时没有为 a 参数提供参数或传递 null，那么 a 的类型将返回“undefined ”,因此 a 的测试类型！== "number "将计算为 true，a 将被设置为默认值 5。否则，测试将失败，我们使用提供的数字。
我们本来可以测试 if `typeof a === "undefined"`，但是通过对“number”进行测试，我们确保即使用户向我们的函数传递了一个字符串，比如“hello”，默认的数值也将用于变量。通过这种方式，我们的功能变得更加强大，能够检测并解决不合理的输入。

## 总结和结束语

概括一下，这里是我们在这篇文章中所做的一些重点。

1.  我们从说明函数是 JavaScript 编程中需要掌握的最重要的概念之一开始。
2.  然后，我们看了使用 JavaScript 函数在代码组织和重用方面给我们带来的一些好处。
3.  我们继续看如何定义和使用函数。具体来说，我们看到一个函数由 4 部分组成:
    *   function 关键字
    *   函数名
    *   (可选)参数的逗号分隔列表
    *   函数体-在花括号{ }之间
4.  我们看到函数可以使用 return 语句向调用者返回值。
5.  我们学习了一个函数是通过使用它的名字后跟括号( )来调用的。函数的参数(如果有)在括号内传递。
6.  我们了解到，创建执行单一、专门任务的函数被认为是一种良好的实践。
7.  最后，我们了解到，在符合 ES6 的浏览器中，可以用默认参数值定义函数。我们通过在条件语句中巧妙使用`typeof`操作符，为旧版本的 JavaScript 提供了一个替代实现。

总之，掌握 JavaScript 函数是成为一名经验丰富的 JavaScript 开发人员的重要组成部分。这篇文章只是对 JavaScript 函数的介绍。我鼓励您更详细地探索 JavaScript 函数。您作为 JavaScript 开发人员脱颖而出的能力取决于此。你未来的自己会感谢你的努力。