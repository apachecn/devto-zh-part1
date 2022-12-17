# 初学者用飞镖

> 原文：<https://dev.to/jvarness/dart-for-beginners-bc3>

我最近写了一篇关于 Angular 的文章，以及用 Dart 和 TypeScript 编写 Angular 应用程序的区别。

如果你想写一个 Angular 应用程序，我设法找到了一些关于如何编写 [TypeScript](https://dev.to/abelagoi/learn-the-basis-of-typescript-to-start-writingangular-87i) 代码的好东西，但我注意到没有关于 [Dart](https://www.dartlang.org/) 的教程，所以我决定写一个！

## 先决条件

在开始本教程之前，您应该为您需要的操作系统安装 Dart。如果不想安装 Dart，可以尝试使用 [DartPad](https://dartpad.dartlang.org/) 。这就像是[艾莉](https://ellie-app.com/)或者 [JsFiddle](https://jsfiddle.net/) 但是对于 Dart 来说。

对于本教程，我假设您将在检查模式下运行 Dart 代码。例如，如果我有一个名为`hello.dart`的 dart 文件，在命令行上我会像这样运行我的代码:

```
$ dart hello.dart -c 
```

Enter fullscreen mode Exit fullscreen mode

Dart 中的“Checked”或“strong”模式在编译和运行代码时强制执行类型检查。这将是 Dart 未来的[标准](https://www.dartlang.org/dart-2.0#strong-mode-and-static-typing)，所以最好现在就掌握强模式。

## `hello world!`

如果你学过任何编程语言，你可能会发现 Dart 的 hello world 程序与许多其他语言非常相似:

```
void main() {
  print('hello world!');
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我定义了一个返回`void` (nothing)的函数`main`，它调用一个打印“hello world！”的`print`函数去控制台！

Dart 使用闭包的力量使它成为一行代码成为可能([阅读这里](https://www.dartlang.org/guides/language/language-tour#functions)了解更多信息):

```
void main() => print('hello world!'); 
```

Enter fullscreen mode Exit fullscreen mode

## 镖基础知识

Dart 支持[七种不同的内置类型](https://www.dartlang.org/guides/language/language-tour#built-in-types)。使用它们也很容易:

```
void main() {
  String name = 'Bob'; // text
  String apple = '\u{1F34E}'; // one character
  String orange = '\u{1F34A}';
  int numApples = 5; // integer value
  double numOranges = 1.5; // floating point value

  print('$name has $numApples  $apple and $numOranges  $orange');

  bool hasLots = (numApples + numOranges) > 5.0;

  if(hasLots) {
    print('$name has a lot of fruits!!');
  } else {
    print('$name needs more fruits...');
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，发生了很多事情。我定义了一个名为`name`的`String`，值为`"Bob"`。我还定义了几个 unicode 字符，Bob 拥有的每种水果的编号，我使用字符串插值打印出所有信息。

通过将`$`作为变量名称的前缀，并将其直接放入字符串中，我可以让 Dart 打印我想要它打印的内容，而不需要自己连接字符串。

我还使用数学运算符[将两个数字相加。您可以使用许多运算符。](https://www.dartlang.org/guides/language/language-tour#operators)

Dart 还有[流控制语句](https://www.dartlang.org/guides/language/language-tour#control-flow-statements)，允许你在代码中有分支行为。像`if`和`else`这样的语句可以让你的代码根据不同变量的状态执行不同的操作。在本例中，`hasLots`的计算结果为`true`，因此执行`if`块中的`print`语句，而不是`else`块中的语句。

***要尝试的事情:**把`name`改成自己的名字。请尝试将这些数字更改为其总和不等于 5 的数字。将`apple`或`orange`更改为`"\u{1F953}"`，注意打印报表变得多么不准确。将每种类型更改为`var`，注意没有任何变化。*

## 定义自己的类型

假设您想要设计可以帮助您表示形状的类。我们希望能够计算二维形状的面积和周长。

在 Dart 中，你可以以一个[类](https://www.dartlang.org/guides/language/language-tour#classes) :
的形式定义你想要的行为

```
class Shape {
  double area() => 0.0;
  double perimeter() => 0.0;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常愚蠢的形状...它实际上什么都不做。所有的行为都被归零。在 Dart 中，这就是如何创建一个**接口**。

Dart 没有针对接口的特殊关键字。本质上，你只需要在一个类中创建默认行为，然后在扩展类中使用它。

例如，如果我想写一个圆形和矩形的类，我可以这样写:

```
class Circle implements Shape {
  final double _radius;

  Circle(this._radius); // Circle constructor

  @override
  double area() => PI * pow(this._radius, 2);

  @override
  double perimeter() => 2 * (PI * this._radius);
}

class Rectangle implements Shape {
  final double _height, _width;

  Rectangle(this._height, this._width); // Rectangle constructor

  Rectangle.square(double size) : this(size, size); // alternate constructor

  @override
  double area() => this._height * this._width;

  @override
  double perimeter() => (this._height * 2) + (this._width * 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了很多事情:`Circle`和`Rectangle`都是`implements Shape`，这意味着那些类**的实例都是** `Shape`的。它们都有一个`area()`和`perimeter()`方法，但是它们的创建方式不同:

```
void main() {
  Shape circle = new Circle(5.0);
  Shape rectangle = new Rectangle(4.0, 3.0);
  Shape square = new Rectangle.square(3.3);

  List<Shape> shapes = [circle, rectangle, square];

  shapes.forEach((shape) {
    print("This shape has an area of ${shape.area()} and a perimeter of ${shape.perimeter()}");
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`Rectangle`类有两个构造函数:默认值接受一个`height`和一个`width`，另一个名为`square`的构造函数将`Rectangle`的`height`和`width`设置为相同的值。

虽然`square`构造函数看起来像是作为一个函数被调用，但它并不是真正的函数。您仍然必须使用`new`关键字，因为您仍然在用**实例化**一个对象，但是它不是一个函数，所以它不能作为函数被调用。如果你删除了`new`关键字，就会导致编译时错误。

## 包装完毕

我希望这个介绍是好的！如果你想更多地了解 Dart,[语言之旅](https://www.dartlang.org/guides/language/language-tour)将是你拥有的最有用的资源。如果你想让我更深入地了解某件事，请在评论中告诉我，或者问一个# explainlikeimfive 问题，也许我能帮上忙:)