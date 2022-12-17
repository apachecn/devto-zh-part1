# 类的问题:检查 JavaScript 中的类结构

> 原文：<https://dev.to/andychiare/a-matter-of-class>

*本文原载于我的[中简介](https://medium.com/@andrea.chiarelli)*

自从*类*结构成为官方 JavaScript 语法的一部分以来，已经一年半多了。由 ECMAScript 2015 规范引入的这一构造受到了许多开发人员的热烈欢迎。事实上，它被认为是模拟类、继承和其他面向对象特性的各种尝试正式化的标志，这些特性由许多库提供了好几年，如 [Prototype](http://prototypejs.org/) 、 [ExtJS](https://www.sencha.com/products/extjs/) 、 [Dojo](https://dojotoolkit.org/) 、 [Backbone](http://backbonejs.org/) 、 [Ember](http://emberjs.com/) 等。TypeScript 本身的诞生就是为了在 JavaScript 语言中引入基于类的面向对象编程。然而，*类*结构可能会导致对 JavaScript 面向对象本质的严重误解。我们来分析一下为什么。

### 一个方便的构造

使用*类*构造非常方便。只需看看下面的代码来定义*动物*类:

```
class Animal {
  constructor(name, legsNumber) {
    this.name = name;
    this.legsNumber = legsNumber;
  }

  speak() {
    console.log(this.name + â€˜ makes a noise.');
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们将其与等效的 ECMAScript 5 代码进行比较:

```
var Animal = function (name, legsNumber) {
  this.name = name;
  this.legsNumber = legsNumber;
};

Animal.prototype.speak = function() {
  console.log(this.name + â€˜ makes a noise.');
}; 
```

Enter fullscreen mode Exit fullscreen mode

*类*构造提供了一个更加紧凑的代码。对于具有传统面向对象编程背景的人来说，它看起来是独立的和熟悉的。

当我们使用继承时，这种构造的便利性变得更加明显，通过分析下面的代码我们可以看到，其中的*蛇*和马类是通过从*动物* :
派生来定义的

```
class Snake extends Animal {
  constructor(name: string) {
    super(name, 0);
  }

  speak() {
    console.log(this.name + â€˜ hisses.');
  }
}

class Horse extends Animal {
  constructor(name: string) {
    super(name, 4);
  }

  speak() {
    console.log(this.name + â€˜ whinnies.');
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们用亲爱的老 ECMAScript 5:
来考虑各自的定义

```
var Snake = function (name) {
  Animal.apply(this, [name, 0]);
};

Snake.prototype = Object.create(Animal.prototype);
Snake.prototype.constructor = Snake;
Snake.prototype.speak = function() {
  console.log(this.name + â€˜ hisses.');
};

var Horse = function (name) {
  Animal.apply(this, [name, 4]);
};

Horse.prototype = Object.create(Animal.prototype);
Horse.prototype.constructor = Horse;
Horse.prototype.speak = function() {
  console.log(this.name + â€˜ whinnies.');
}; 
```

Enter fullscreen mode Exit fullscreen mode

使用*类*结构的便利似乎是显而易见的:除了更少的语句，我们还获得了更好的可读性和与大多数面向对象编程语言语法的相似性。无论如何，不管使用什么样的定义，在这两种情况下我们都将以同样的方式使用类和构造函数:

```
var kaa = new Snake(“Kaa the Python”);
var fury = new Horse(“Fury the Horse”);

console.log(kaa.legsNumber);    //0
console.log(fury.legsNumber);   //4 
```

Enter fullscreen mode Exit fullscreen mode

这确保了新旧方法之间的最大互操作性。

### 不是每个人都快乐

尽管它很方便，但是，并不是所有的开发人员都对 class 构造的引入很感兴趣。对于大多数程序员来说，它看起来很熟悉，给人一种使用基于类的面向对象编程语言(如 Java 和 C #)的错觉。

但这就是问题的关键:这是一种幻觉，一种危险的幻觉。与看起来相反，ECMAScript 2015 规范并没有在 JavaScript 中引入类的概念。JavaScript 是并且仍然是一种基于原型的面向对象编程语言，即使使用了*类*构造。

在 JavaScript 语法中引入这种结构似乎延长了这种语言定义的错误链:从名称本身的选择，这已经导致(并且仍然导致)Java 的一点混乱，到使用*新的*关键字来创建对象，[被道格拉斯·克洛克福特](http://javascript.crockford.com/prototypal.html)积极地争论。

这些选择的主要影响是，程序员在对语言的真正理解上被误导了，并被引导去责备它，因为它的行为不是所期望的。

### 是真正的阶级吗？

但是基于类的语言中的*类*结构和 JavaScript 中的有什么区别呢？正如我们所说，JavaScript 仍然是一种基于原型的语言，所以*类*构造只是以更紧凑的方式创建构造函数的*语法糖*。如果我们试图得到一个类的类型，我们会发现它只是一个函数:

```
console.log(typeof Animal);   //function 
```

Enter fullscreen mode Exit fullscreen mode

使用*类*构造定义*动物*、*蛇*和*马*，与使用旧 ECMAScript 5 风格代码定义具有相同的效果。对象不是通过引用一个类来创建的，而是通过引用一个原型，即另一个对象来创建的，这在 JavaScript 中很常见。这意味着我们可以动态地改变对象的结构，而不涉及它的原始类。例如，我们可以编写下面的代码:

```
fury.wingsNumber = 2;

console.log(fury instanceof Horse);   //true 
```

Enter fullscreen mode Exit fullscreen mode

我们给我们的*马*对象添加了一对翅膀，它仍然是一匹马。这在 JavaScript 中完全合法，但是在基于类的世界中很难实现和理解。

更糟糕的是，我们可以推翻我们的*马*对象的整个结构如下:

```
var furySnake = new Horse();

furySnake.legsNumber = 0;
furySnake.speak = function() {
  console.log(this.name + â€˜ hisses.');
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们重新定义了一匹*马*的结构，就像它是一条*蛇*一样，但是它仍然是*马*类的一个实例。这超出了类的任何定义，不仅在编程语言的上下文中，而且在哲学和集合论的上下文中。

例如，从数学的角度来看，一个类是一个对象的集合，这些对象可以由所有成员共享的一个或多个属性明确定义。显然，由于*愤怒*和*愤怒蛇*没有共同的属性，它们不能属于同一个(概念上的)类。同样，从基于类的面向对象的角度来看，这两个对象不能属于同一个类。

### 结论

对于来自 Java、C#和 C++等语言的开发人员来说，选择除了 *class* 之外的名字可能听起来很奇怪，但这可能会减少错觉和失望。

无论如何，现在木已成舟。重要的是 JavaScript 开发人员知道*类*的真正含义。你已经被警告了…

Andrea Chiarelli 是《掌握面向对象编程的 JavaScript》一书的作者 (Packt 出版社)。