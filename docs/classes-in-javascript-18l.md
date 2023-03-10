# JavaScript 中的类

> 原文：<https://dev.to/kayis/classes-in-javascript-18l>

当你几年前开始编写 JavaScript 时，你可能知道它没有**类**，而是有**原型**！所以你查阅了大学的 OOP 课程，试图找到一些关于这方面的东西，你的教授对此的唯一想法是，没有人使用基于原型的面向对象，你可以使用基于类的面向对象。

所以，你知道了 JavaScript 是这个星球上最常用的编程语言之一，并且得到了你的第一份编码工作，但是现在你的知识一文不值？

许多年来，这都是事实。

但是...！

随着 ES6(又名 ES2015)的发布，类是 JavaScript 的一部分，不再需要考虑原型。

**这是什么意思？**

JavaScript 中的类基本上是整个基于面向对象原型的东西的语法糖，第一个版本(如 ES2015)只包括非常基础的东西。你可能从基于类的语言中了解到的一些东西不能开箱即用。

### 那么 ES2015 有什么好用的呢？

```
// You can create a class like this
class A {}

// But also like that
const A = class {};
let A = class {};

// It can even be anonymous if you export it from a module
export default class {}

// Your class can also have methods and static methods
class A {
  static doAnything() {
    console.log(this, "== undefined");
  }

  doSomething() {
    console.log("I'm doing something with ", this);
  }
}

// You can also use inheritance, with calls to the parent class
class B extends A {
  constructor() {
    super();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这并不多，但是它给了你基于类的 OOP 的基础，并且它在后台摆弄着原型，所以你不必担心。

### 那么，你想要那种可能缺失的东西？

**类字段**是许多语言的基本特性，但*不是 JS 类的直接*部分。所以下面的对 ES2015 不起作用。

```
class A {
  x: 10; // Error
} 
```

Enter fullscreen mode Exit fullscreen mode

你必须在创建时在`constructor`中定义它们。

```
class A {
  constructor() {
    this.x = 10;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

有一个[提议](https://github.com/tc39/proposal-class-fields)在 ES2018 中加入它们，它们看起来有点不同。

```
class A {
  x = 10;
} 
```

Enter fullscreen mode Exit fullscreen mode

绑定方法是许多语言的默认行为，但是 JS 类的方法在默认情况下是不绑定的。这意味着，当您将方法作为回调传递时，它们对应的对象可能会丢失。

```
class A {
  constructor(x) { this.x = x; }
  publicMethod() { return this.x; }
}

const a = new A(10);
const callback = a.publicMethod;
callback(); // Error 
```

Enter fullscreen mode Exit fullscreen mode

这可以通过**手动绑定**一个方法到`this`构造函数中来解决。

```
class A {
  constructor(x) {
    this.x = x;
    this.publicMethod = this.publicMethod.bind(this);
  }
  publicMethod() { return this.x; }
}

const a = new A(10);
const callback = a.publicMethod;
callback(); // 10 
```

Enter fullscreen mode Exit fullscreen mode

在未来(ES2018)，这也可以通过**类字段**和**箭头功能**来解决。

```
class A {
  constructor(x) { this.x = x; }

  // a class field that holds a method
  publicMethod = () => { return this.x; };
}

const a = new A(10);
const callback = a.publicMethod;
callback(); // 10 
```

Enter fullscreen mode Exit fullscreen mode

这是因为**箭头函数**捕获了其封闭范围的上下文(`this`)并且在函数定义的`{}`中，范围是类的实例。

### 结论

基于类的编程在每个 ECMAScript 版本中都有越来越多的特性，所以如果你不喜欢原型，在这个基础上构建是值得的。

目前它们还不尽如人意，但是已经足够开始了。