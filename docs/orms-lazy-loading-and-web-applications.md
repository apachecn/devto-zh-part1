# ORMs、延迟加载和 Web 应用程序

> 原文：<https://dev.to/ruidfigueiredo/orms-lazy-loading-and-web-applications>

这三样东西有什么共同点？

它们经常一起使用。这是个好主意吗？不，一点也不，这就是我在这篇博文中要说服你的。

[![Screen stuck on loading](img/abc77457ae01c06f008a4057d7d4eca3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--pewl9wS1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f3mazbr3uecy0ype3er0.jpg)

但是首先让我们谈一谈 ORM 是什么，它是做什么的，以及在 ORM 环境中什么是惰性加载。

ORM 是“对象关系映射器”的缩写。ORM 的两个流行例子是实体框架和 Hibernate。

在关系数据库(如 Sql Server、MySql 等)中，数据用表、列和行来表示。还有约束的概念，如主键和外键、索引和许多其他在面向对象编程语言中没有直接对等物的东西。

在 ORM 流行之前，如果你需要从数据库中读取数据，你需要编写一个 SQL 查询，SQL 代码将与你的源代码放在一起。

ORMs 改变了这一点，它为我们提供了一种声明性地指定数据库中的数据如何映射到面向对象结构的方法。例如，表如何映射到类，列如何映射到属性。

有了这个中间层，现在就有可能编写看起来只是操纵对象的代码，但是在幕后，它被转换成透明地发送到数据库的 SQL 查询。

这很棒，因为它简化了我们与数据交互的方式。以前，我们必须编写 SQL 语句，这些语句实际上是我们代码中的字符串。将 SQL 与用户输入连接在一起是非常常见的，这也是 SQL 注入的基本功能。当用户在文本框中编写 SQL 语句而不是普通输入，并最终在数据库中执行时，就会发生 SQL 注入。

这大概就是 ORM 的内容。

懒装怎么办？

为了真正理解延迟加载，我们需要多谈一点 ORMs 中的概念，称为*上下文*(或*会话*)。每当 ORM 加载数据并将其转换为内存中的对象时，这些对象就存储在*上下文*中。

通常，涉及从数据库获取数据的交互首先创建一个上下文，然后执行触发数据获取的操作。下面是一个使用实体框架从数据库中获取所有客户的例子:

```
var myContext = new MyContext();
var allCostumers = myContext.Customers.ToList();
//... 
```

Enter fullscreen mode Exit fullscreen mode

在访问上下文中的 Customers 属性并调用*之后。ToList()* (这是触发从数据库获取数据的操作)，客户数据存储在上下文本身中(例如，在实体框架中，您可以在 *myContext 中访问它。客户.本地*。这样，如果您对客户进行了更改，ORM 就可以找出更改的内容，并生成适当的 SQL 语句。

环境也有能力给你一个稍微改变的客户版本。当*延迟加载*被启用时，这一点尤其重要。假设数据库中的客户表有许多订单。对应的客户类可能是这样的:

```
public class Customer 
{
    public int CustomerId {get; set;}   

    //...

    public virtual ICollection<Order> Orders { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意订单集合中的*虚拟*关键字。这只是意味着您可以创建 customer 的子类并覆盖该属性的功能。如果您启用了延迟加载，这正是上下文要做的事情。

启用延迟加载后，上下文将跟踪该属性是否被访问。当第一次访问它时，上下文将透明地获取相关数据(在本例中是客户的订单)。

这允许我们像这样写代码:

```
var myContext = new MyContext();
var johnDoe = myContext.Customers.Single(customer => customer.Name == "John Doe");

foreach(var order in johnDoe.Orders)
{
    //do something with John Doe's order
} 
```

Enter fullscreen mode Exit fullscreen mode

这一切看起来都很好。它很容易阅读和理解，但它可以更有效。然而，除非你熟悉你正在使用的特定 ORM 是如何工作的，否则原因并不明显。

在这种情况下，这是一个实体框架，在调用*时将在其中获取数据。单身(...)*和枚举完*命令*时。所以在这个例子中有两个数据库调用。

很容易使情况变得更糟，并产生所谓的 N+1 问题。这里有一个例子:

```
var myContext = new MyContext();
var thisYearCustomers = myContext.Customers.Where(customer => customer.JoinYear == 2017);

foreach(var customer in thisYearsCustomers)
{
    foreach(var order in customer.Orders)
    {
        //do something with the customer's order
    }       
} 
```

Enter fullscreen mode Exit fullscreen mode

这将在枚举客户时触发数据库请求(foreach over customers ),然后针对每个客户的订单。因此，你可以选择 N+1 或 1+N。

如果你认为懒惰装载是可怕的，从来没有意义，在这种情况下，你可能是对的。导致 N+1 问题的情况很可能总是一个错误。然而，这并不意味着惰性加载是没有用的。

想象一个桌面应用程序，其中上下文是通过**几次**用户交互来创建和生存的。例如，用户打开一个客户屏幕，查看它，然后决定查看该客户的订单。

在这种情况下，延迟加载非常方便而且有意义。

当它不在 web 应用程序中时。这是因为在一次以上的用户交互中，上下文将不存在。这不可能。

在 web 应用程序中，用户的操作会导致一个 HTTP 请求从用户的浏览器发送到服务器。然后，服务器对该请求进行所有必需的处理，并向用户发回响应。然后对每个用户动作重复这个过程。

在两次请求之间，服务器会忘记用户，因此，如果创建了一个上下文来响应用户的操作，那么在发送响应之后，它就会消失。这就是网络的无状态本质。

在 web 应用程序中使用延迟加载唯一可以实现的事情就是可以避免额外的数据库调用。这是因为，如果用户请求特定客户的订单，运行在服务器上的代码必须在处理用户请求的过程中加载客户和订单，它可以在一个或两个数据库调用中完成。惰性加载只是使它很容易在没有意识到的情况下结束在两个数据库调用场景中。

为了完整起见，我将只提到如果在大多数 ORM 中禁用延迟加载会发生什么。在上面的例子中，Orders 属性将是空的。您必须指示 ORM 与客户一起一次性获取它。这就是所谓的急切装载:

```
var myContext = new MyContext();
var johnDoe = myContext.Customers.Include(customer => customer.Orders).Single(customer => customer.Name == "John Doe");

foreach(var order in johnDoe.Orders) //no extra db access
{
    //do something with John Doe's order
} 
```

Enter fullscreen mode Exit fullscreen mode

在 web 应用程序中使用延迟加载并没有什么好处，但是在“野外”使用它比不使用它要常见得多。即使我们使用有时可能更方便的论点，出现严重性能问题(如 N+1)的可能性抵消了任何可能的好处。