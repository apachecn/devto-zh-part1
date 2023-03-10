# 原型介绍- Js

> 原文：<https://dev.to/danny/intro-to-prototype---js-35a>

# 原型

###### 本帖是对 Js - Prototype 对象的介绍。

每当我们用 javascript 创建一个函数时，javascript 引擎都会为我们创建两个对象。

Js 引擎处理函数时创建的两个对象是:

1.  返回函数本身的函数对象，

2.  原型对象。

### 原型是什么

Js 中的 prototype 主要用于继承，您可以在函数的 prototype 属性上添加方法和属性，以使这些方法和属性对该函数的实例可用。

```
function foo() {
console.log("Hello!!");
}
// foo.prototype 
```

每当 Js 引擎创建 prototype 对象时，它也会在函数中创建一个名为 prototype 的属性。借助 function 中的这个 prototype 属性，我们可以访问为该函数创建的 prototype 对象。

例如，我们见过 foo 函数，它有一个指向原型对象的名为 prototype 的属性。为了利用原型对象，我们使用 new 关键字创建一个对象。

```
var myObj = new foo();
// myObj 
```

当我们创建一个对象时，myObj Js 引擎会为我们创建一个对象，默认情况下，它有一个名为“ **proto** 的对象。它又指向由该函数创建的初始原型对象。

### 使用 Proto

现在我们知道，每当我们创建一个对象来查看它如何工作时，Js engine 都会创建一个 proto 对象，让我们在“myObj”对象中创建一个属性“say”，值为“Hi from myObj itself”。

```
function foo(){}
var myObj = new foo();
myObj.say = "Hi from myObj itself"; 
```

当我们访问“myObj.say”时，我们知道它将从 myObj 本身返回值“Hi”。我们为原型对象创建了另一个对象，具有相同的属性，但是具有不同的值“Hi from proto object”

```
function foo(){}
var myObj = new foo();
myObj.__proto__.say = "Hi from proto object"; 
```

> 访问“myObj。 **proto** 。say”会返回“Hi from proto object”。

```
function foo(){}
var myObj = new foo();
myObj.say = "Hi from myObj itself";
// Hi from myObj itself
myObj.__proto__.say = "Hi from proto object";
// Hi from proto object
delete myObj.say;
// true
myObj.say
// Hi from proto object 
```

但是当我们删除" myObj.say "并访问它时，Js 引擎会查看" **proto** "对象，看是否有任何适当的" say"。它将找到属性“myObj”。**原型**。say”，其值为“Hi from proto object ”,这将在访问“myObj.say”时返回。

> 既然我们知道无论何时创建一个对象，Js engine 都会为我们创建另一个名为 prototype 的对象。我们可以使用“访问原型。原型”从功能。但是当我们使用 new 关键字创建一个对象时，Js 会创建“ **proto** ”，它指向原型对象。
> 
> 每当我们在一个对象中寻找一个属性时，Js 引擎都会返回它——如果它在对象本身中找到它的话。如果没有这样的属性，Js 引擎将查找原型对象。