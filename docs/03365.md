# 我们来谈谈 JavaScript 中的硬绑定

> 原文：<https://dev.to/metcoder/lets-talk-about-hard-binding-in-javascript-9m2>

在忙碌了一周后，我来到这里，我想和你们(*读者*)谈谈 JavaScript 中一些令人困惑的( *at less for me* )和有趣的概念。
目前，我加入了 facebook 上的不同群组，在那里我们主要讨论编程。建议、趋势、新闻以及更多相关信息；很明显，全是西班牙语(*我来自墨西哥如果你不知道*)。

其中一个小组专门研究 Node.js 和 JavaScript ( *)如果你说西班牙语，我邀请你加入。下面是[环节](https://www.facebook.com/groups/node.es)* )。看了一会帖子，发现这个代码:

```
function foo() {
console.log(this.bar);
}
var obj = { bar: "bar" };
var obj2 = { bar: "bar2" };
var orig = foo;
foo = function(){ orig.call(obj); };
foo();
foo.call(obj2); 
```

Enter fullscreen mode Exit fullscreen mode

花点时间想想这段代码将打印出什么，然后继续阅读本文。我等你，不要担心。

[![Thinking about it](img/c7afcf77150c4aa84415f36dbfd3ad93.png)T2】](https://i.giphy.com/media/xf20D8HzvTQzu/giphy.gif)

不管您是否得到它，这段代码片段连续打印了两次`bar`。

这里的[链接](https://jsfiddle.net/greenpioneer/x16gxwk7/)看到它，是的，是他的代码。

嗯，我们得到这个结果是因为一件事:**硬绑定**

[![Ok?](img/7cc827c80bf79c003c0b43f85ed04362.png)T2】](https://i.giphy.com/media/3o7btPCcdNniyf0ArS/giphy.gif)

## 还有什么是硬绑定？

对硬绑定有一个很好的理解是很重要的，在得到一个硬绑定之前，要了解一些容易混淆的东西。其中之一就是**这个**。

### 这个

是 OOP 编程范例中最常用的一种机制。在这种情况下，我们谈论正在创造的对象的背景。比如他的函数和全局变量的使用。在 JS 中，历史是一些令人困惑的，但不是另一个世界。

在 JS 中，`this`是一种基于上下文的机制，它反过来基于函数调用，而不是在声明函数的地方。当一个函数被调用时，一个**调用栈**被创建，通过这个调用栈，一种记录也被创建。在这个记录中保存了函数的信息，包括调用的方式和位置、传递的参数等等。有了所有这些信息，`this`就是这些属性之一。最后，在函数执行期间，记录仍然有效。

### 装订

在 JS 中，**我们调用**函数的上下文非常重要。

想想这个函数:

```
function something(){
  var a = 2;
  console.log(this.a);
}

var a = 5;

something(); 
```

Enter fullscreen mode Exit fullscreen mode

要打印什么？

我让您尝试这些代码，并解释到底发生了什么。

当我们调用`this`时，我们正在调用全局对象的上下文。换句话说，我们将属性(*，在这里是变量* `a`)声明为函数外部的全局属性。不管我是否创建了一个和外部变量一样的变量，我从全局对象中获取属性，而不是从函数上下文中获取。

#### 隐含绑定

那么这段代码呢。要打印什么？

```
function something(){
  console.log(this.a);
}

var a = 5;

var obj = {
  a: 10,
  something: something
}

obj.something() 
```

Enter fullscreen mode Exit fullscreen mode

当我们有了这个，我们就应用了一个叫做**隐式绑定**的东西。。。*那我凭什么吃那个？*

正如你在这个例子中看到的，我们将上下文作为一个对象分配给函数。更简单地说，我们将函数的上下文创建为一个对象。因此，不要考虑全局环境，而是考虑我们制作的对象的环境。

随之而来的问题是，如果我们这样做呢？

```
function something(){
  console.log(this.a);
}

var obj = {
  a: 10,
  something: something
}

var cake = obj.something

var a = 5;

cake(); 
```

Enter fullscreen mode Exit fullscreen mode

我们失去了对象上下文，又有了全局上下文，但是为什么呢？还记得我告诉过你**祈祷很重要**吗？嗯，这就是原因。

我们丢失了对象的上下文，因为我们将函数赋给了一个变量，获得了对该函数的引用，改变了上下文。当我们这样做的时候，一个**隐式丢失**发生，我们获得全局上下文(对于这个例子，是*)。*

如果我们这样做，也会发生同样的情况:

```
function something(){
  console.log(this.a);
}

function anotherFunction(fn){
  fn();
}

var obj = {
  a: 10,
  something: something
}

var a = 'Five';

anotherFunction(obj.something); 
```

Enter fullscreen mode Exit fullscreen mode

记住，**调用关系到**

#### 显式绑定

正如所有事情一样，每个问题都有一个解决方案(*或者我们可以制定一个解决方案，但在这种情况下，有一个默认的*)。

如果我们想要强制一个函数在没有隐式引用的情况下获得一个对象的特定上下文，我们可以使用像`call(...)` ( *[更多信息](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call)* )或`apply(...)` ( *[更多信息](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)* )这样的函数。

调用`call()`并传递想要应用的对象作为参数，强制函数使用对象的上下文。比如像这样:

```
function something(){
  console.log(this.a);
}

var obj = {
  a: 10,
}

var a = 'Five';

something.call(obj); 
```

Enter fullscreen mode Exit fullscreen mode

如上，我们可以用`apply(this, [])`来代替。将对象和数组作为参数传递。

## 硬装订

此时，我希望一切都好。我想你明白我写的一切。现在有了这个，也许你想知道:**还有一个硬装订，那个呢？**

好吧，让我告诉你一些事情。使用这种解决方案，我们不能解决所有的绑定问题，你知道吗？

我们回到我找到的第一个代码:

```
function foo() {
console.log(this.bar);
}
var obj = { 
  bar: "bar" 
}
var obj2 = { 
  bar: "bar2" 
}
var orig = foo;
foo = function(){ 
  orig.call(obj);
}
foo();
foo.call(obj2); 
```

Enter fullscreen mode Exit fullscreen mode

为什么这么做？

把注意力就放在这一行:`foo = function() { orig.call(obj) }`
好了，你看到了吗？那就是被**硬捆绑的**。

该函数使得`foo`每次被调用时总是执行相同的操作，不管调用上下文是什么。**创建一个函数，在内部手动调用一个显式绑定，并强制执行相同的指令**,不管你在哪里以及如何调用该函数

正如我们上面所说的，**每个问题都有一个解决方案** ( *或者我们可以做一个* ):

```
function foo(something) {
  console.log(this.bar + something);
  return this.bar + something
}

var obj = { 
  bar: 2
}

var obj2 = {
  bar: 4
}

function bind(fn, obj){
  return function(){
    return fn.apply(obj, arguments)
  }
}

var result = bind(foo, obj);

var result_2 = bind(foo, obj2);

result(2);

result_2(2); 
```

Enter fullscreen mode Exit fullscreen mode

或者，我们可以使用 **ES5** 给我们
的那个

```
function foo(something) {
  console.log(this.bar + something);
  return this.bar + something
}

var obj = { 
  bar: 2
}

var result = foo.bind(obj);

result(2); 
```

Enter fullscreen mode Exit fullscreen mode

这个工具是由 **ES5** 在`Function.prototype.bind` ( *[更多信息](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)* )中介绍的，它以一种更简单的方式给了我们**硬绑定**。

# 还有一件事

请随意对本文进行评论和更正。我写这篇文章只是为了更清楚地解释和理解绑定是如何工作的以及什么是硬绑定。如果你想了解更多关于绑定或 JavaScript 的知识，你可以阅读 [You-Dont-Know-JS](https://github.com/getify/You-Dont-Know-JS) 并更清楚地理解这一点和更多关于 JS 的东西。

再见。