# C#中的重载运算符

> 原文：<https://dev.to/jonstodle/overloading-operators-in-c-e7>

C#是一种成熟的语言，在这一点上，有一些特性被该语言的用户遗忘了，甚至没有发现。我以前写过我最喜欢的“晦涩”特性之一:[隐式类型化数组语法](https://dev.to/jonstodle/the-underrated-new---in-c-8g-temp-slug-1954861)。

当你创建自己的类时(就像你经常做的那样)，你可以设置 C#中不同的操作符应该如何处理这个类。虽然不允许创建新的操作符，但是可以重载大多数现有的操作符。

让我们从一个简单的类开始，它表示二维空间中的一个点:

```
public class Point  
{
    public double X { get; set; }
    public double Y { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们声明两点，以有所作为:

```
var point1 = new Point { X = 1, Y = 4 };  
var point2 = new Point { X = 2, Y = 3}; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想把这两个点相加得到`point3`，我们必须这样做:

```
var point3 = new Point  
{
    X = point1.X + point2.X,
    Y = point1.Y + point2.Y
}; 
```

Enter fullscreen mode Exit fullscreen mode

这非常冗长和笨拙。尤其是如果你要经常做这个手术的话。

让我们通过重载加号运算符来简化它。让我们将这个方法添加到`Point` :
的类定义中

```
public static Point operator +(Point lhs, Point rhs)  
    => new Point
    {
        X = lhs.X + rhs.X,
        Y = lhs.Y + rhs.Y
    }; 
```

Enter fullscreen mode Exit fullscreen mode

我们定义了一个将返回一个`Point`的静态方法，但是我们没有像往常一样给它一个名字，而是说它是一个操作符，然后说它是哪个操作符。参数是要添加的两个点。然后我们返回新点。

值得注意的是，只要求其中一个参数的类型与包含类型相同；也就是说，你可以在一个`Point`和一个`double`之间为加号运算符定义一个重载，例如:

```
public static Point operator +(Point lhs, double rhs)  
    => new Point
    {
        X = lhs.X + rhs,
        Y = lhs.Y + rhs
    }; 
```

Enter fullscreen mode Exit fullscreen mode

你有能力在这里发疯。

我们现在可以更简洁地声明`point3`:

```
var point3 = point1 + point2;  
// X: 3
// Y: 7 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过给`point3` :
添加一个`double`来给`X`和`Y`添加`2`

```
var point4 = point3 + 2;  
// X: 5
// Y: 9 
```

Enter fullscreen mode Exit fullscreen mode

其他一些可以重载的二元运算符(两个操作数之间的运算符)有`-`、`*`、`/`、`%`。您可以在[文档](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/overloadable-operators)中查看完整列表。

接下来，让我们为`==`操作符添加一个重载。这样更容易检查两个点是否在同一个地方。

重载`==`的时候也要重载`!=`。此外，**强烈建议**优先于`Equals()`和`GetHashCode()`。

这两个运算符和两个方法都与相等以及如何确定两个对象是否相等有关。通过覆盖它们，我们确保它们都以相同的方式计算相等。

让我们从`==`操作符开始:

```
public static bool operator ==(Point lhs, Point rhs)  
    => lhs.X == rhs.X && lhs.Y == rhs.Y; 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，这和以前几乎一样。如果`X`和`Y`的值相等，我们接收 to 点并返回 true。这也使得`!=`操作符易于实现:

```
public static bool operator !=(Point lhs, Point rhs)  
    => !(lhs == rhs); 
```

Enter fullscreen mode Exit fullscreen mode

只是一个被否定的`==`。

接下来，为了安抚 C#神们(包括[乔恩·斯基特](https://www.quora.com/Who-is-Jon-Skeet-and-how-did-he-become-famous-on-Stack-Overflow)，让我们实现`Equals()`和`GetHashCode()` :

```
public override bool Equals(object obj)  
{
    if (obj is Point point)
    {
        return this == point;
    }

    return false;
}

public override int GetHashCode() => new { X, Y }.GetHashCode(); 
```

Enter fullscreen mode Exit fullscreen mode

`GetHashCode()`实现来自关于如何最好地实现`GetHashCode()`的[堆栈溢出回答](https://stackoverflow.com/a/4630550/1339804)。

现在我们可以很容易地检查 to 点是否相等。让我们声明`point5`，它具有与`point4` :
相同的`X`和`Y`值

```
var point5 = new Point { X = 5, Y = 9 }; 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以做一些测试:

```
point4 == point5 // true  
point4 != point4 // false  
point4 == point3 // false 
```

Enter fullscreen mode Exit fullscreen mode

这不是一个非常全面的测试，但是对于这个演示来说已经足够了。

当然，您也可以重载其他等式运算符:`<`、`>`、`<=`、`>=`。你可以在[文档](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/statements-expressions-operators/overloadable-operators)中看到更多相关内容。

我想展示如何重载的最后一种操作符是转换操作符。这些是从一种类型到另一种类型进行隐式和显式转换时使用的运算符。

例如，您可以通过编写以下代码将*隐式地*将`int`转换为`double`:

```
double myDouble = 1; 
```

Enter fullscreen mode Exit fullscreen mode

虽然`1`是一个`int`，但是`double`类型知道如何从`int`创建一个`double`，并且可以自动为您创建。

然而，走另一条路并不是自动的。你必须指定你想通过做一个*显式*造型:
来强制`double`成为一个`int`

```
int myInt = (int)myDouble; 
```

Enter fullscreen mode Exit fullscreen mode

`int`知道如何从一个`double`创建一个`int`，但是这样做意味着您可能会丢失一些信息，即点号后面的数字，并迫使您显式地告诉它进行转换。

从另一种类型到另一种类型的转换应该是隐式的还是显式的，这取决于每种类型，但是经验法则是，隐式转换应该只在可以确保不丢失信息的情况下进行。显式转换适用于您可以说“您可以这样做，但是……”。 *but* 表示显式转换。

现在让我们看看如何在我们的`Point`类中实现这个漂亮的特性。为了使创建具有相同的`X`和`Y`值的`Point`变得容易，我们应该能够只提供一个`double`，并隐式地将其转换为`Point` :

```
public static implicit operator Point(double d)  
    => new Point { X = d, Y = d }; 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，它看起来非常像前面的重载。不同之处在于“返回类型”变成了`implicit`，方法的名称是转换后返回的类型。有点奇怪它们是如何被调换的，但是你看。

这意味着我们现在可以这样做:

```
Point point6 = 6;  
// X: 6
// Y: 6 
```

Enter fullscreen mode Exit fullscreen mode

这实际上比你想象的要聪明一点:`6`实际上是一个`int`，但是我们的`Point`类只支持从`double`的转换。然而，因为`int`可以被*隐式地*转换成`double`，这是由`Point`支持的，所以 C#会自动进行转换。

为了演示显式转换，我们将提供从`Point`到`double`的转换。当进行这种转换时，`double`将代表`Point`和原点`0,0`坐标之间的直线长度。我们希望这种转换是显式的，因为有信息损失:

```
public static explicit operator double(Point p)  
    => ((p.X * p.X) + (p.Y * p.Y)) / 2; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以这样做了吗:

```
double length = (double)point5;  
// length: 53 
```

Enter fullscreen mode Exit fullscreen mode

通过重载转换操作符，您可以提供一种在自定义类和任何其他类之间进行转换的简单方法。

虽然我们的`Point`类可能应该是一个`struct`，但我特意使用了一个`class`来演示等式运算符重载。在结构中，这些被不同地处理:它们检查被比较的两个结构上的每个属性是否相等。如果它们都相等，则结构被认为是相等的。虽然我们的`Point`类做同样的事情，但我只是想展示它是如何手动完成的。

展开这个可以找到`Point`类的完整代码。

```
public class Point  
{
    public double X { get; set; }
    public double Y { get; set; }

    // Plus operator overload
    public static Point operator +(Point lhs, Point rhs)
        =&gt; new Point
        {
            X = lhs.X + rhs.X,
            Y = lhs.Y + rhs.Y
        };

    public static Point operator +(Point lhs, double rhs)
        =&gt; new Point
        {
            X = lhs.X + rhs,
            Y = lhs.Y + rhs
        };

    // Equality operator overload
    public static bool operator ==(Point lhs, Point rhs)
        =&gt; lhs.X == rhs.X &amp;&amp; lhs.Y == rhs.Y;

    public static bool operator !=(Point lhs, Point rhs)
        =&gt; !(lhs == rhs);

    public override bool Equals(object obj)
    {
        if (obj is Point point)
        {
            return this == point;
        }

        return false;
    }

    public override int GetHashCode() =&gt; new { X, Y }.GetHashCode();

    // Conversion operator overload
    public static implicit operator Point(double d)
        =&gt; new Point { X = d, Y = d };

    public static explicit operator double(Point p)
        =&gt; ((p.X * p.X) + (p.Y * p.Y)) / 2;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在前进，让操作员超载！

编码快乐！