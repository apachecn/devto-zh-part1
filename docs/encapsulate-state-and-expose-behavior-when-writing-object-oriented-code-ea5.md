# 编写面向对象的代码时封装状态并公开行为

> 原文：<https://dev.to/scottshipp/encapsulate-state-and-expose-behavior-when-writing-object-oriented-code-ea5>

阿派。

你听说过吗？不知为什么，我流口水了。

[![A delicious fresh-baked pie](img/23b7597ed7e8a108cf7a505316994e17.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tq6cipru--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2i7wrncftg78xajwdwks.jpeg)

A-PIE 不仅是一种美味的烤甜点，有着薄片状的外壳和温暖的粘性内部，它还是一个帮助记住面向对象编程(OOP)的四个最重要原则的缩写/助记符:

*   一个 b 的抽取。
*   **P** 多态现象。
*   我继承了。
*   封装。

**封装**就是我今天要讲的。似乎函数成了新的令人兴奋的闪亮的东西，每个人都忘记了 OOP，甚至在 OOP 语言中。让我们稍微重置一下。如果你用面向对象的语言写(这是很多的！)，如果 OOP 原则被融入到你的语言设计中，那么最糟糕的事情之一就是忘记它们。这就像拿着一把刀，试图用刀柄去切奶酪。你会被切掉的！

另一种说法是，这就像你开着一辆 85 年的本田思域沿着电线小道行驶，而你旁边有一条铺好的路。这是另一个时代的故事，但我和一个朋友尝试过一次，让我告诉你我的经验:它并不漂亮，除非你想看到一些弯曲的金属...

不好意思！在这里离题了。让我们回到*封装*。

所以“数据类”的概念最近变得相当流行。Kotlin 直接支持数据类，Scala 支持*案例类。这些是 JVM 语言。Java 没有直接的对等物，但有趣的是，这两种方法基本上都是创建经典的 *JavaBean* 的捷径，JavaBean* 是带有公共访问器和赋值器的一包数据的别称。

C#与*自动属性*有着相同的想法。如果你看一下自动属性的[文档，今天有一个**美中不足**的主要例子。在这个例子中，定义了一个`Customer`类，然后这个类被类外的代码变异:](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/auto-implemented-properties)

**客户类别**

```
// This class is mutable. Its data can be modified from
// outside the class.
class Customer
{
    // Auto-Impl Properties for trivial get and set
    public double TotalPurchases { get; set; }
    public string Name { get; set; }
    public int CustomerID { get; set; }

    // other code elided . . . 
} 
```

Enter fullscreen mode Exit fullscreen mode

**程序** :

```
class Program
{
    static void Main()
    {
        // Intialize a new object.
        Customer cust1 = new Customer ( 4987.63, "Northwind",90108 );

        //Modify a property
        cust1.TotalPurchases += 499.99;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

他们甚至在那里放了一个注释:*这个类是可变的。它的数据可以从类外部修改。*

惭愧，惭愧，惭愧我知道你的名字 C#！！！！！！

这就是简单的**面向对象 101！**当目标是跟踪总购买量时，将该功能公开为 Customer 类的公共行为，并封装使该行为成为可能所需的数据。代码应该类似于:

**客户类别**

```
// This class' data cannot be modified from outside the class.
class Customer
{
    private double TotalPurchases;
    private string Name;
    private int CustomerID;

    // other code elided . . . 

    public void AddPurchase( DollarAmount amt ) {
        TotalPurchase += amt;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**程序** :

```
class Program
{
    static void Main()
    {
        Customer cust1 = new Customer ( 4987.63, "Northwind", 90108 );
        cust1.AddPurchase(499.99);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

看看`Customer`类的属性现在是如何`private`、`private`、`private`？

没错！你应该保护你的隐私。很明显，这不仅是我不得不继续教给我两岁孩子的*一课，也是适用于所有年龄段程序员的*相关*和*永恒*格言。*

现在，有各种各样的理由来保护你的隐私。一个很大的问题是，这使得程序更容易进化！很有可能我们会希望在未来从仅仅追踪一个`DollarAmount`发展到更多的东西。相反，我们可能希望存储一个完整的事务列表。

所以我不知道你是怎么想的，但是我宁愿被狠狠地戳一下眼睛，也不愿意花一下午的时间去追踪所有对`TotalPurchases`的引用，把它们从另一个程序员迷失的混乱的杂草中拽出来。在一个好的 OO 设计中，我们保持`TotalPurchases`私有，这是一个一次性的改变。我将简单地更新`Customer`类的内部结构，并免费获得所有的进化！

当然，这真的是你的选择。没有人能告诉你如何写你的代码。如果你认为在你的应用程序中把“数据类”和“行为类”分开不会有问题，那就想尽一切办法好好做吧。但是相信我，即使有一次看着你的手指插在电灯插座里的感觉很有趣，你也不会想再做一次。如果我能帮你省去麻烦，那么我会觉得写这篇文章是尽了我的职责。祝你好运！