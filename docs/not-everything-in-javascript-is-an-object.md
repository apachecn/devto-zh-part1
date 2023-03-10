# JavaScript 中的一切都是对象

> 原文：<https://dev.to/d4nyll/not-everything-in-javascript-is-an-object>

> 这篇文章最初发表于[酿酒出版社](http://blog.brew.com.hk/not-everything-in-javascript-is-an-object/)
> 
> 对于那些只想知道答案的人，可以直接跳到最后的“总结”部分。

关于 JavaScript 是面向对象编程(OOP)语言还是函数式语言，有很多困惑。事实上，JavaScript 两者都可以。

但是这导致人们问“所有东西都是 JavaScript 对象吗？”，“函数呢？”

这篇文章将澄清这一切。

## 让我们从头开始

在 JavaScript 中，有六种原始数据类型:

*   布尔- `true`或`false`
*   `null`
*   `undefined`
*   `number`—双精度 64 位浮点型。**JavaScript**中没有整数。
*   `string`
*   `symbol`(ES6 中新增)

除了这六个基本类型，ECMAScript 标准还定义了一个`Object`类型，它只是一个键值存储。

```
const object = {
  key: "value"
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，简而言之，任何不是原始类型的东西都是`Object`，这包括函数和数组。

> 所有函数都是对象。

```
// Primitive types
true instanceof Object; // false
null instanceof Object; // false
undefined instanceof Object; // false
0 instanceof Object; // false
'bar' instanceof Object; // false

// Non-primitive types
const foo = function () {}
foo instanceof Object; // true 
```

Enter fullscreen mode Exit fullscreen mode

## 原始类型

基本类型没有附加的方法；所以你永远看不到`undefined.toString()`。也正因为如此，基本类型是不可变的，因为它们没有附加可以改变它的方法。

你可以将一个原始类型重新赋值给一个变量，但它将是一个新的值，旧的值不会也不能被改变。

```
const answer = 42
answer.foo = "bar";
answer.foo; // undefined 
```

Enter fullscreen mode Exit fullscreen mode

> 原始类型是不可变的

此外，基本类型存储为值本身，不像对象存储为引用。这在执行相等检查时有所暗示。

```
"dog" === "dog"; // true
14 === 14; // true

{} === {}; // false
[] === []; // false
(function () {}) === (function () {}); // false 
```

Enter fullscreen mode Exit fullscreen mode

> 基本类型通过值存储，对象通过引用存储

## 功能

函数是一种特殊类型的对象，有一些特殊的属性，比如`constructor`和`call`。

```
const foo = function (baz) {};
foo.name; // "foo"
foo.length; // 1 
```

Enter fullscreen mode Exit fullscreen mode

而且就像一个普通的对象一样，你可以给对象添加新的属性:

```
foo.bar = "baz";
foo.bar; // "baz" 
```

Enter fullscreen mode Exit fullscreen mode

这使得函数成为一等公民，因为它可以作为参数传递给其他函数，就像任何其他对象一样。

##### 方法

方法是一种对象属性，也是一种函数。

```
const foo = {};
foo.bar = function () { console.log("baz"); };
foo.bar(); // "baz" 
```

Enter fullscreen mode Exit fullscreen mode

## 构造函数

如果您有几个共享相同实现的对象，您可以将该逻辑放在构造函数中，然后使用构造函数来创建这些对象。

构造函数和其他函数没有什么不同。当函数用在`new`关键字之后时，它被用作构造函数。

> *任何*函数都可以是构造函数。

```
const Foo = function () {};
const bar = new Foo();
bar; // {}
bar instanceof Foo; // true
bar instanceof Object; // true 
```

Enter fullscreen mode Exit fullscreen mode

构造函数将返回一个对象。你可以在函数体内使用`this`给对象分配新的属性。因此，如果我们想让许多对象的属性`bar`初始化为值`"baz"`，那么我们可以创建一个新的构造函数`Foo`来封装这个逻辑。

```
const Foo = function () {
  this.bar = "baz";
};
const qux = new Foo();
qux; // { bar: "baz" }
qux instanceof Foo; // true
qux instanceof Object; // true 
```

Enter fullscreen mode Exit fullscreen mode

> 您可以使用构造函数来创建新的对象。

在没有 new 的情况下运行一个构造函数，比如`Foo()`，会像普通函数一样运行`Foo`。`this`函数内部会对应执行上下文。所以如果我们在所有函数之外调用`Foo()`，它实际上会修改`window`对象。

```
Foo(); // undefined
window.bar; // "baz" 
```

Enter fullscreen mode Exit fullscreen mode

相反，将普通函数作为构造函数运行通常会返回一个新的空对象，正如您已经看到的那样。

```
const pet = new String("dog"); 
```

Enter fullscreen mode Exit fullscreen mode

### 包装对象

由于`String`、`Number`、`Boolean`、`Function`等功能，产生了混淆。当用`new`调用时，它为这些原始类型创建*包装器对象*。

`String`是一个全局函数，当被传入一个参数时，它创建一个**原语**字符串；它会尝试将参数转换成字符串。

```
String(1337); // "1337"
String(true); // "true"
String(null); // "null"
String(undefined); // "undefined"
String(); // ""
String("dog") === "dog" // true
typeof String("dog"); // "string" 
```

Enter fullscreen mode Exit fullscreen mode

但是你也可以使用`String`函数作为构造函数。

```
const pet = new String("dog")
typeof pet; // "object"
pet === "dog"; // false 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个新的代表字符串`"dog"`的**对象**(通常称为*包装器对象*)，具有以下属性:

```
{
  0: "d",
  1: "o",
  2: "g",
  length: 3
} 
```

Enter fullscreen mode Exit fullscreen mode

> 对象包装器也经常被称为包装对象。去想想。

## 自动装箱

有趣的是，原始字符串和对象的构造函数都是`String`函数。更有趣的是，你可以在原始字符串上调用`.constructor`，而我们已经知道原始类型**不能**有方法！

```
const pet = new String("dog")
pet.constructor === String; // true
String("dog").constructor === String; // true 
```

Enter fullscreen mode Exit fullscreen mode

正在发生的是一个叫做*自动装箱*的过程。当你试图在某些原语类型上调用一个属性或方法时，JavaScript 会先将其转换成一个**临时** *包装器对象*，并访问其上的属性/方法，不影响原。

```
const foo = "bar";
foo.length; // 3
foo === "bar"; // true 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，为了访问属性`length`，JavaScript 将`foo`自动装箱到包装器对象中，访问包装器对象的`length`属性，然后丢弃它。这是在不影响`foo` ( `foo`仍然是一个原始字符串)的情况下完成的。

这也解释了为什么当您试图将一个属性赋给一个基本类型时，JavaScript 不会报错，因为赋值是在那个临时包装器对象上完成的，而不是基本类型本身。

```
const foo = 42;
foo.bar = "baz"; // Assignment done on temporary wrapper object
foo.bar; // undefined 
```

Enter fullscreen mode Exit fullscreen mode

如果您尝试使用没有包装对象的原始类型，比如`undefined`或`null`，它会报错。

```
const foo = null;
foo.bar = "baz"; // Uncaught TypeError: Cannot set property 'bar' of null 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

1.  并非 JavaScript 中的所有东西都是对象
2.  JavaScript 中有 6 种基本类型
3.  所有不是原始类型的都是对象
4.  函数只是一种特殊类型的对象
5.  函数可以用来创建新的对象
6.  字符串、布尔值和数字可以表示为原始类型，也可以表示为对象
7.  某些基本类型(字符串、数字、布尔值)表现得像对象一样，这是因为有一个叫做自动装箱的 JavaScript 特性。