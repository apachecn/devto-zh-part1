# JavaScript 关闭

> 原文：<https://dev.to/kayis/javascript-closes>

当我开始使用 JavaScript 时，我的大部分编码经验来自于 PHP、C 和 Java。所以当我看到 JavaScript 的第一个片段时，它对我来说就像是巫术。到处定义的函数，放入变量中，到处移动，他们甚至使用看起来不属于他们的变量。

谁调用这些函数？

他们从哪里得到变量？

我甚至不...

# 引入闭包

所以你可能读过闭包是 JavaScript 中的一个东西。嗯，他们可能是主要的事情。

## 第一:功能

那么理解它们需要什么呢？

你大概习惯了简单的静态(？)其他语言的函数定义。它们也存在于 JS 中。

```
function f(x) {
  return x + 10;
}

f(10); // -> 20 
```

Enter fullscreen mode Exit fullscreen mode

但是也有函数表达式。

表达式是一种语言结构，它(有点)返回一些东西，你说它们*对一些东西*求值。例如`if`或`for`不是表达方式，你就不能写`let x = if (b > 10) ...`。函数调用、计算或比较都是表达式。

```
f(10); // returns something -> 20
10 * 3; // evaluates to 30
10 === 3; // evaluates to false 
```

Enter fullscreen mode Exit fullscreen mode

在 JS 中，不仅函数调用，而且它们的定义也可以是表达式。

```
let f = function myFunction() {}; // evaluates to a reference to the function myFunction

let g = function () {}; // The function doesn't even have to have a name
                        // because we can call it via its reference stored in g

g(); 
```

Enter fullscreen mode Exit fullscreen mode

这允许我们在某个地方定义函数，保存在一个变量中，并在我们的应用程序中发送，例如，如果你想用它作为一个回调。

```
let f = function () {}; // store function reference

window.addEventListener("load", f); // call it when something happens 
```

Enter fullscreen mode Exit fullscreen mode

正如你从其他语言中知道的，有全局变量和局部变量。

```
x = 20;

function f(y) {
  let z = 10;
  return x * y * z;
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`x`没有定义`var`、`let`或`const`，所以它将成为一个全局变量。在 JS 中，这意味着它将被附加到浏览器中的全局`window`对象上。全局变量可以在任何地方被任何函数访问，所以我们可以在`f()`内部使用它。

## 第二:闭包

这和闭包有什么关系？

唔**闭包是*关闭*或*捕获它们的*定义上下文的函数**。这听起来有点神秘，但意味着他们可以使用在周围定义的*变量。*

有点像 globals。

```
x = 10; // global

function f() { // -- definition context of g
  let y = 20; // local to f, but g has access

  let g = function (z) {
    return x * y * z; // global * context * local
  }

  return g;
}              // -- end of g's def. context

function h(a) {  
  return x * a; // no access to y, just global and local
}

let newG = f();

newG(2); 
```

Enter fullscreen mode Exit fullscreen mode

你为什么要用这个？例如，对于回调。

假设您想要访问一些非全局数据，但是作为回调传递的函数不会接收这些数据。

`` JavaScript
let user = { name:" Kay-is " }；`

 `//传递给 setTimeout 的函数不会得到任何参数
//但是它可以访问其定义
周围的变量 setTimeout(function(always undefined){
alert(user . name)//alert 是全局的
// user 是父作用域
}，100)
`

 `或者想象一下，你有一些数据，不应该被修改。

`` JavaScript
function ReadOnly(user){
//参数对 ReadOnly 是局部的
// getName 和 getAge 是可以访问它们的闭包
return {
getName:function(){ return user . name；}，
getAge:function(){ return user . age；}
}
}`

 `//某处保存
let peter = {name: "Peter "，age:43 }
let readOnlyPeter = ReadOnly(Peter)；

//对 peter 有访问权限的代码可以修改对象
//对 readOnlyPeter 有访问权限的代码不能修改
readOnlyPeter . getname()；
readonlypeter . getage()；
readonlypeter . name = " Franz "；// readOnlyPeter 没有此属性。
`

 `## 结论

闭包可以被称为 JavaScript 的面包和黄油。JavaScript 是一个基于事件的系统，几乎所有的事情都是通过回调异步处理的，甚至承诺或可观察到的事情都是对这个事实的抽象。

它们允许在不需要全局变量的情况下编写代码，但仍然保留了一点全局变量给编程带来的轻量级感觉。