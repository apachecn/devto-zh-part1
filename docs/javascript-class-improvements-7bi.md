# JavaScript 类改进

> 原文：<https://dev.to/kayis/javascript-class-improvements-7bi>

*[Flickr 上克里斯蒂安·科伦的封面图片](https://www.flickr.com/photos/christiaancolen/)*

正如你可能注意到的，JavaScript 在 ES2015 更新中得到了真正的类，但许多人仍然希望对经典 OOP 有更好的支持。定义实例字段的方法相当麻烦，而且没有办法使字段私有。

这一切都将随着这个新提议而改变:[类字段](https://github.com/tc39/proposal-class-fields)

## 为什么

在 JavaScript 中定义类相当简单。

```
class A {} 
```

Enter fullscreen mode Exit fullscreen mode

但是给出该类字段的实例有点奇怪。

```
class A {
  constructor() {
    this.field = 123;
  }
}

const a = new A();

console.log(a.field); 
```

Enter fullscreen mode Exit fullscreen mode

此外，该字段现在是公共的，因此每个人都可以访问和修改它。

目前的惯例是在一个字段前加上`_`来标记它为私有，但是这与其说是一个解决方案，不如说是一个技巧。

## 如何

这个新提议允许在没有构造函数帮助的情况下定义实字段。

```
class A {
  field = 123;
}

const a = new A();

console.log(a.field); 
```

Enter fullscreen mode Exit fullscreen mode

这些字段甚至可以相互访问。

```
class A {
  x = 10;
  y = 10 + this.x;
}

const a = new A();

console.log(a.y); 
```

Enter fullscreen mode Exit fullscreen mode

这很有效，因为整个班级都喜欢这样。

```
var A = function A() {
  this.x = 10;
  this.y = 10 + this.x;
}; 
```

Enter fullscreen mode Exit fullscreen mode

同样，这个特性可以用来定义方法，这些方法总是有正确的`this`。

```
class A {
  x = 10;
  m = () => console.log(this.x);
}

const a = new A();

const callback = a.m;

setTimeout(callback, 100); // 10 
```

Enter fullscreen mode Exit fullscreen mode

这个*解糖*成这样:

```
var A = function A() {
  var _this = this;
  this.x = 10;
  this.m = function () {
    return console.log(_this.x);
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

甚至有允许私有字段的想法:

```
class A {
  #a = 10;
  m = () => this.#a;
}

const a = new A();

console.log(a.a);

console.log(a.m()); 
```

Enter fullscreen mode Exit fullscreen mode

可悲的是，这似乎并没有实现，甚至在巴别塔也没有实现。

## 结论

越来越多的经典 OOP 特性进入了 JavaScript。虽然我不是 OOP 的最大粉丝，并且发现基于原型的方法更灵活，但我认为这将使来自经典 OOP 语言的开发人员更容易使用 JavaScript。