# C# 7 中漂亮而有用的特性

> 原文：<https://dev.to/kleinrein/nifty-and-useful-features-in-c-7>

*这篇文章最初发表在 Steemit 网站上。*

令人愉快的编程语言 C#的第七版并不是一个大版本，但它提供了一些漂亮的特性和一些非常棒的语法。这篇文章将概述我认为最有用的特性。

# 元组

元组已经存在了相当一段时间，但在此之前它还没有被集成到语言中。元组是具有特定数量和序列的元素的数据结构。现在，您可以声明具有多个返回值的方法。

如果你认为你必须接受 sum 并把它算进 GetFoo，你并不孤单。乍一看，语法确实有点奇怪。

**举例:**

```
public (int sum, int count) GetFoo()
{
    return (1, 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 解构

您可以将一个元组解构为单独的部分。虽然有不同的方法可以达到同样的目的。

**举例:**

```
// Explicit
(int sum, int count) = GetFoo();

// var
(var s, var c) = GetFoo();

// var shorthand
var (s, c) = GetFoo();

// Existing variables
int sum, count;
(sum, count) = GetFoo();

// Deconstructing objects also works
class Student
{
    public string Name { get; set; }
    public int Age { get; set; }

    public void Deconstruct(out string name, out int age)
    {
        name = Name;
        age = Age;
    }
}

var student = new Student { FirstName = "Peter", Age = 22 };
var (name, age) = person; 
```

Enter fullscreen mode Exit fullscreen mode

### 元组初始化

```
var student = ("Peter", 22);
Console.WriteLine($"Name: {student.Item1}, Age: {student.Item2}");

// Assign item names
var student = (name: "Peter", age: 22);
Console.WriteLine($"Name: {student.name}, Age: {student.age}"); 
```

Enter fullscreen mode Exit fullscreen mode

# 局部功能

局部函数是在方法内部定义的函数，可以访问该方法中的所有局部变量。在 C#7 之前，你可以声明一个 Func 来实现这一点，但是这是不必要的昂贵，而且看起来很难看。局部函数的好处是编译器将它转换成一个常规的私有静态方法，所以实际上没有开销。

**举例:**

```
static void Main(string[] args)
{
    int Sum(int x, int y)
    {
        return x + y;
    }

    Console.WriteLine(Sum(10, 20));
    Console.ReadKey();
} 
```

Enter fullscreen mode Exit fullscreen mode

值得一提的是，本地函数支持令人愉快的 lambda 语法，您也可以声明一个与现有私有方法同名的本地函数。

# 调出变量

out var 声明是在使用 TryParse 时提高可读性的语法糖。

**举例:**

*之前:*

```
int value;
if (int.TryParse(input, out value)) 
{
    Foo(value);
} 
else 
{
    Foo(value);
}
Foo(value); 
```

Enter fullscreen mode Exit fullscreen mode

***现在:***

```
if (int.TryParse(input, out var value)) 
{
    Foo(value);
}
else 
{
    Foo(value);
}
Foo(value); // Value is still in scope 
```

Enter fullscreen mode Exit fullscreen mode

# 数字分隔符

在 C#7 中，我们使用下划线 _ 作为数字分隔符。这纯粹是语法糖，但它确实提高了大量数据的可读性。

**举例:**

```
int bin = 0b1001_1010_0001_0100;
int num = 1_2__2_3_4_5_6_3; // Separators is simply ignored 
```

Enter fullscreen mode Exit fullscreen mode

## 模式匹配

在 C#7 中，我们得到了模式匹配。毫无疑问，随着每一个新版本的发布，C#变得更加实用。

### *是*

is 运算符用于检查对象是否与特定类型兼容，例如，是否实现了指定的接口。在 C#7 中，你可以使用 is 来检查模式。

**举例:**

*之前:*

```
string s = o as string;
if(s != null)
{
    // Do something with s
} 
```

Enter fullscreen mode Exit fullscreen mode

*现在:*

```
if(o is string s)
{
    // Do something with s
}; 
```

Enter fullscreen mode Exit fullscreen mode

### *开关*

switch 语句也得到了增强。使用 case，您可以检查 const 模式(这在以前是可能的)、立即声明指定类型的变量的类型模式和 var 模式。

您还可以使用 when 关键字来增强模式过滤器，使其仅在满足特定条件时应用。这是 C#6 中已经用于异常过滤器的同一个关键字。

**举例:**

```
public static void SwitchPattern(object o)
{
    switch (o)
    {
        case null:
            Console.WriteLine("It's a constant pattern");
            break;
        case int i:
            Console.WriteLine("It's an int");
            break;
        case Person p when p.FirstName.StartsWith("An"):
            Console.WriteLine($"a An person {p.FirstName}");
            break;
        case Person p:
            Console.WriteLine($"Any other person {p.FirstName}");
            break;
        case var x:
            Console.WriteLine($"it's a var pattern with the type {x?.GetType().Name}");
            break;
        default:
            break;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 了解更多

如果你想更深入地了解 C#7 的新特性，有很多材料可以阅读。

*   [C# 7.0 在 StackOverflow 文档中的特性](https://stackoverflow.com/documentation/c%23/1936/c-sharp-7-0-features#t=201708041026258916375)
*   [c# 7 的新特性](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-7)
*   [c# 7.0 的新特性](http://www.c-sharpcorner.com/article/all-about-c-sharp-7-features/)