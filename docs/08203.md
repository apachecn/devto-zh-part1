# 理解 JavaScript 中的提升

> 原文：<https://dev.to/imwiss/understanding-hoisting-in-javascript>

你懂 JavaScript，但是你真的*懂* JavaScript 吗？这是一门伟大的语言，尽管有些人可能会有不同的看法。当然，它有一些不好的地方，但在过去几年里它已经有了很大的改进，开发者在正确使用 JavaScript *和遵循最佳实践方面变得越来越好。严格模式在防止新开发人员犯一些糟糕的 JavaScript 错误和不幸遇到不必要的行为方面也变得越来越好。*

然而，并不是每个人都听说过**吊装**这个术语或者知道它的意思。在本文中，我将解释什么是提升，并展示不同的例子，以便您可以更好地理解它的全部内容。

[![Learning](img/68f7b9837586329bb90876f3b6055202.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nNSrQyfW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.designingforscale.com/conteimg/2017/03/laptopAndBooks.png)

#### JavaScript 解释器

当您执行 JavaScript 代码时，解释器会检查代码两次。

第一次运行代码是对代码进行安全检查和小的优化。安全检查，如确保语法正确，是否有对`eval`或`with`的调用，等等。然后，它尽可能地优化代码，以确保在执行时有更好的性能。这也是提升发生的地方(稍后会有更多的介绍)，被称为*编译*运行。

第二次运行是指它实际执行你的代码，一行一行地检查，做赋值，调用函数，等等。

#### 什么是吊装？

提升是指 JavaScript 解释器将所有变量和函数声明移动到当前作用域的顶部。重要的是要记住，只有实际的声明才会被提升，而赋值则留在原处。

提升是在解释器第一次运行代码时完成的。

#### 变量声明

让我们从一个基本的例子开始，看看下面的代码:

```
'use strict';

console.log(bar); // undefined
var bar = 'bar';
console.log(bar); // 'bar' 
```

起初，您可能认为示例代码会在第 3 行(`console.log(bar);`)抛出一个`ReferenceError`，因为`bar`还没有被声明。然而，有了提升的魔力，它不会扔出一个`ReferenceError`但是在那个点上`bar`的值将会是`undefined`。这是因为 JavaScript 解释器第一次运行整个代码，并在当前作用域的顶部声明所有变量和函数，然后在第二次运行时执行代码。

解释器第一次运行后，相同的代码会是这样的:

```
'use strict';
var bar;
console.log(bar); // undefined
bar = 'bar';
console.log(bar); // 'bar' 
```

请注意`bar`现在是如何在顶部(`var bar`)声明的，但在那一点上还没有赋值？这是一个微妙但重要的区别，这就是为什么`bar`被登录为`undefined`，而不是抛出一个`ReferenceError`。

#### 函数声明

提升也适用于*函数声明*(不是*函数表达式*)。让我们来分析下面的示例代码:

```
'use strict';

foo();
function foo() {
    console.log(bam); // undefined
    var bam = 'bam';
}

console.log(bam); // ReferenceError: bam is not defined 
```

在这个示例代码中，我们能够成功地调用函数`foo`，因为它是一个*函数声明*，因此它被提升到当前作用域的顶部。然后，`foo`在调用它的时候会输出`undefined`，因为和前面的例子一样，`bam`被提升到了它当前作用域的顶部，也就是`function foo()`。这意味着`bam`是在调用`console.log(bam)`之前声明的，但是它还没有被赋值(`bam = 'bam'`)。

然而，这里需要注意的重要一点是，`bam`被提升到了它的**当前**范围的顶部。这意味着**不是在全局范围内声明的**,而是在函数范围内声明的。

解释器第一次运行后，相同的代码会是这样的:

```
'use strict';

function foo() {
    var bam;
    console.log(bam); // undefined
    bam = 'bam';
}

foo();
console.log(bam); // ReferenceError: bam is not defined 
```

注意`foo()`是如何被移动到顶部的，并且`bam`是在`foo()`中声明的？这意味着，当你在第 10 行调用`console.log(bam)`时，它不会在一般范围内找到变量`bam`，会抛出一个`ReferenceError`。

#### 函数表达式

接下来，我想介绍的第三个用例是，相对于*函数声明*，函数表达式*如何不被提升。相反，是它们的变量声明被提升。这里有一些示例代码来证明我的观点:* 

```
'use strict';

foo();
var foo = function () {
    console.log(bam); // undefined
    var bam = 'bam';
} 
```

这段代码抛出了一个`TypeError: foo is not a function`错误，因为**只有**变量声明`var foo`被提升到了文件的顶部，而函数对`foo`的赋值只有在解释器第二次运行时才完成。

解释器第一次运行后，相同的代码会是这样的:

```
'use strict';

var foo;
foo(); // `foo` has not been assigned the function yet
foo = function () {
    console.log(bam);
    var bam = 'bam';
} 
```

#### 什么优先？

最后，我想介绍的最后一个用例是*函数声明*被悬挂在**变量**之前。让我们看看下面的代码:

```
'use strict';

console.log(typeof foo); // 'function'

var foo = 'foo';

function foo () {
    var bam = 'bam';
    console.log(bam);
} 
```

在这个例子中，`typeof foo`返回`function`而不是`string`，即使函数`foo()`是在变量之后声明的。这是因为*函数声明*在*变量声明*之前被提升，所以`foo = 'foo'`在第二次运行时被执行，**在**之后我们称之为`typeof foo`。

第一次运行时，解释器将把`foo()`提升到当前作用域的顶部，然后到达`var foo = 'foo'`行。在这一点上，它意识到`foo`已经被声明了，所以它不需要做任何事情，并将继续它的第一次代码运行。

然后，在第二次运行时(基本上是*执行*代码)，它会在到达赋值`foo = 'foo'`之前调用`typeof foo`。

解释器第一次运行后，相同的代码会是这样的:

```
'use strict';

function foo () {
    var bam = 'bam';
    console.log(bam);
}

console.log(typeof foo); // 'function'
foo = 'foo'; 
```

#### ES6

ES6 是未来的发展方向，也是大多数开发人员将会使用的方向，所以让我们看看提升是如何应用于 ES6 代码的。

正如我们在上面看到的，与`var`变量相比，`let`和`const`变量的提升方式不同。然而，`let`和`const`变量仍然被提升，不同之处在于，在运行时赋值完成之前，它们不能被访问。

来自 [ES6 的文档](https://tc39.github.io/ecma262/#sec-let-and-const-declarations):

> 变量在其包含的词法环境被实例化**时被创建，但是在变量的 LexicalBinding 被评估之前不能以任何方式被访问。**

说到底，这是一个小小的技术问题，解释器在编译运行时对这些变量进行提升，但在赋值发生之前访问它们时，它们会抛出引用错误，本质上阻止我们在赋值之前访问这些变量。

#### 结论

我希望这阐明了提升在 JavaScript 中是如何工作的。这肯定没有听起来那么棘手或复杂，但它确实需要我们分解不同的用例，并尝试不同的场景来理解事情是如何在幕后工作的。

如果你有任何意见或问题，请不要犹豫给我，我很乐意听到你的反馈。

*本帖最初发表于[为规模](https://designingforscale.com/?utm_source=hoisting&utm_medium=guest-post&utm_campaign=devto)设计*