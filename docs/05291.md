# 典型的 JavaScript 面试练习(解释)

> 原文：<https://dev.to/maxpou/typical-javascript-interview-exercises-explained>

几周前，我在自己的 twitter feed 上发现了一篇非常有趣的博文:[“最佳前端 JavaScript 面试问题(由前端工程师撰写)”](https://performancejs.com/post/hde6d32/The-Best-Frontend-JavaScript-Interview-Questions-%28written-by-a-Frontend-Engineer%29)作者是 Boris Cherny。正如你可能猜到的，作者展示了一些在求职面试中要问的有趣问题。问题分为 4 个部分:概念、编码、调试和系统设计。在这里，我将重点介绍**调试**部分。

我真的很喜欢这个问题，因为它们处理了 JavaScript 的特性:对象比较、事件循环、作用域、this、原型继承和 equal 运算符与[抽象相等比较算法](http://www.ecma-international.org/ecma-262/5.1/#sec-11.9.3)。

在阅读解决方案之前，我建议你自己找到答案。

## 练习 1

> 我想让这段代码注销“嘿 amy”，但是它注销了“嘿 Arnold”——为什么？

```
function greet (person) {
  if (person == { name: 'amy' }) {
    return 'hey amy'
  } else {
    return 'hey arnold'
  }
}
greet({ name: 'amy' }) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

**回答**

这里的问题如下:`{ name: 'amy' } != { name: 'amy' }`。当比较两个相等或严格相等的对象时，JavaScript 会比较相关的内部引用。这里，这两个对象具有相同的属性和相同的值。但是在内存中，这是两个不同的对象。

这里的解决方案可能是:

```
function greet (person) {
  if (person.name === 'amy') {
    return 'hey amy'
  }
  return 'hey arnold'
}
greet({ name: 'amy' }) // "hey amy" 
```

Enter fullscreen mode Exit fullscreen mode

## 练习 2

> 我希望这段代码按顺序记录数字 0、1、2、3，但它并没有达到我的预期(这是您偶尔会遇到的一个 bug，有些人喜欢在采访中问这个问题)。

```
for (var i = 0; i < 4; i++) {
  setTimeout(() => console.log(i), 0)
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

**问题**

我喜欢这个，因为它有点复杂，处理范围和 JavaScript 事件循环。

这里的经典陷阱是零延迟 T2。`setTimeout(callback, 0)`并不意味着回调会在零毫秒后被触发。

事件循环端发生的情况如下:

1.  当前调用堆栈被设置为第一个 setTimeout()。
2.  windows.setTimeout()被认为是一个 Web APIs(为了更好的**非阻塞 I/O** )。因此调用堆栈将这部分代码发送给正确的 Web APIs。0 毫秒后，回调(这里是一个匿名函数)将被发送到队列(而不是调用堆栈)。
3.  由于调用堆栈是空闲的，for-loop 可以继续到第二个 setTimeout...(在我们满足这个条件 i < 4 之后重复)...
4.  现在循环结束了`i === 4`。JS 现在可以逐个执行回调队列了。每个 console.log(i)将打印 4。

你觉得失落吗？我希望这部动画能更好地帮助你！

*用[放大镜制作的动画(试试吧很好玩！)](http://latentflip.com/loupe/?code=Y29uc29sZS5sb2coIlN0YXJ0ISIpOwoKZm9yIChsZXQgaSA9IDA7IGkgPCA0OyBpKyspIHsKICBzZXRUaW1lb3V0KGZ1bmN0aW9uICgpIHsKICAgICAgY29uc29sZS5sb2coaSk7CiAgfSwgMCk7Cn0KCmNvbnNvbGUubG9nKCJFbmQhIik7!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)*

第二个问题与范围有关。setTimeout 函数的 4 个实例共享同一个实例`i`。

```
var foo = 'bim'
//                                â–¼ this is a reference to variable foo, not his associated value ('bim') 
var getFoo = function () { return foo }
foo = 'boum'

getFoo() // 'boum' 
```

Enter fullscreen mode Exit fullscreen mode

* * *

**回答**

因此，有几种解决方案可供选择:

*   使用立即调用的函数表达式*又名*life。一旦定义好,“包装函数”就会运行。

```
for (let i = 0; i < 4; i++) {
  (function (i) {
    setTimeout(() => console.log(i), 0)
  })(i)
} 
```

Enter fullscreen mode Exit fullscreen mode

*   切换到`let`关键字(而不是`var`)。这个(新？)关键字使作用域变得更容易理解。

```
for (let i = 0; i < 4; i++) {
  setTimeout(() => console.log(i), 0)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 练习 3

> 我想让这段代码注销“doggo”，但它注销未定义！

```
let dog = {
  name: 'doggo',
  sayName () {
    console.log(this.name)
  }
}
let sayName = dog.sayName
sayName() 
```

Enter fullscreen mode Exit fullscreen mode

* * *

**回答**

先前的代码返回`undefined`。为什么？看，在第一个 let 条件下，我们定义了一个具有两个属性的对象(name 和函数 sayName())。然后在第二个 let 中，我们将属性 sayName 复制到另一个变量中，这是一个函数。然后，我们在她的上下文之外调用这个变量(在全局变量中)。函数 sayName()将返回 window.name(如果环境是节点，则为全局)。还有`typeof window.name === "undefined"`。

*   ðÿ'ž(肮脏的那个)。如果我们想保留 sayName 变量。然后我们需要给狗绑定上下文:

```
sayName.bind(dog)()
// or:
dog.sayName.bind(dog)() 
```

Enter fullscreen mode Exit fullscreen mode

这是脏的，对吗？ðÿ

*   ðÿ'在她的原始上下文中直接调用这个函数

```
let dog = {
  name: 'doggo',
  sayName () {
    console.log(this.name)
  }
}
dog.sayName() // will log "doggo" 
```

Enter fullscreen mode Exit fullscreen mode

## 练习 4

> 我想让我的狗叫()，但是我得到一个错误。为什么？

```
function Dog (name) {
  this.name = name
}
Dog.bark = function () {
  console.log(this.name + ' says woof')
}
let fido = new Dog('fido')
fido.bark() 
```

Enter fullscreen mode Exit fullscreen mode

* * *

**回答**

我们得到了以下错误*类型错误:fido.bark 不是一个函数*。在前面的代码中，我们在另一个函数(`Dog()`)上设置了 bark 函数，它也是一个构造函数。有没有可能因为在 JavaScript 中，函数就是对象。

2 个解决方案:

*   ðÿ'ž(肮脏的那个)。fido.bark 不是一个函数，但`Dog.bark`是。因此，让我们使用这一个，用 function.prototype.bind()解决`this`问题，就像上面的练习一样:

```
var boundedBark = Dog.bark.bind(fido)
boundedBark() // "fido says woof" 
```

Enter fullscreen mode Exit fullscreen mode

但是在我看来，使用 function.prototype.bind() *(几乎总是)*会导致混乱。

*   ðÿ'在狗的原型上设置了 bark()

```
function Dog (name) {
  this.name = name
}

Dog.prototype.bark = function () {
  console.log(this.name + ' says woof')
}

let fido = new Dog('fido')
fido.bark() // "fido says woof" 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以使用 class 关键字(ES2015 ),它只是前面代码的语法糖。

```
class Dog {
  constructor (name) {
    this.name = name
  }

  bark () {
    console.log(this.name + ' says woof')
  }
}

let fido = new Dog('fido')
fido.bark() // "fido says woof" 
```

Enter fullscreen mode Exit fullscreen mode

## 练习 5

> 为什么这段代码会返回这样的结果？

```
function isBig (thing) {
  if (thing == 0 || thing == 1 || thing == 2) {
    return false
  }
  return true
}
isBig(1)    // false
isBig([2])  // false
isBig([3])  // true 
```

Enter fullscreen mode Exit fullscreen mode

* * *

**回答**

我们在这里使用简单的相等运算符(*例如* ==)，与严格比较(*例如* ==)相反。使用这个操作符，不一定要比较相同的类型。

*   `isBig(1)`按预期通过条件`thing == 1`。
*   `isBig([2])`将通过条件`thing == 2`。当比较一个数组和一个数字时，数组将被转换成一个数字。这是[抽象等式比较算法](http://www.ecma-international.org/ecma-262/5.1/#sec-11.9.3)的一部分。根据这个算法，如果我们将一个数与一个对象*(提醒:数组是 JS 中的对象)*进行比较，这个数组就会被转换成一个数组。在这里，只有一个物品在里面所以`[2] == 2`。

因为这个算法对于最普通的开发者来说是晦涩难懂的，所以我们应该避开这个运算符([eslint eqeq 规则是你的朋友ðÿ'](http://eslint.org/docs/rules/eqeqeq))。

```
// weird results
[] == ![]     // true
[] == false   // true

// Non transitive relation
"1" == true   // true
"01" == true  // true
"01" == "1"   // false 
```

Enter fullscreen mode Exit fullscreen mode

## 练习 6(加分)

> 如何保持我英雄榜的不变性？

```
const heroes = [
  { name: 'Wolverine',      family: 'Marvel',    isEvil: false },
  { name: 'Deadpool',       family: 'Marvel',    isEvil: false },
  { name: 'Magneto',        family: 'Marvel',    isEvil: true  },
  { name: 'Charles Xavier', family: 'Marvel',    isEvil: false },
  { name: 'Batman',         family: 'DC Comics', isEvil: false },
  { name: 'Harley Quinn',   family: 'DC Comics', isEvil: true  },
  { name: 'Legolas',        family: 'Tolkien',   isEvil: false },
  { name: 'Gandalf',        family: 'Tolkien',   isEvil: false },
  { name: 'Saruman',        family: 'Tolkien',   isEvil: true  }
]

const newHeroes = heroes.map(h => {
  h.name = h.name.toUpperCase()
  return h
}) 
```

Enter fullscreen mode Exit fullscreen mode

你知道吗？ðŸ™‚