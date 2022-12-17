# 帮帮忙，这些对象形状一样但是不共享一个接口！

> 原文：<https://dev.to/anotherdevblog/help-these-objects-have-the-same-shape-but-dont-share-an-interface-kfl>

我有一点困惑，我似乎在网上找不到任何有人解决这个问题的地方。我要处理的是:

我使用第三方库将一些文档解析成对象。对我来说不幸的是，文档不是 XML 或 JSON，因此需要这个第三方库。更不幸的是，这种文档类型的模式有三种不同的版本，大部分相同，但略有不同。这些差异体现在第三方库返回的对象中。

这里有一个例子来说明我所说的。三个 POCO 类，每个代表一个订单版本:

```
// Order V1
public class ThirdPartyOrderV1
{
  public decimal Amount { get; set; }
  public ThirdPartyOrderItemV1[] LineItems { get; set; }
  public ThirdPartyOrderAddressV1 BillingAddress { get; set; }
  public ThirdPartyOrderAddressV1 ShippingAddress { get; set; }
  public string SomeV1ThingIDontCareAbout { get; set; }
  public decimal AnotherV1ThingIDontCareAbout { get; set; }
  // ...
}

public class ThirdPartyOrderItemV1
{
  public int ItemID { get; set; }
  public int Quantity { get; set; }
  // ...
}

public class ThirdPartyOrderAddressV1
{
  public string Name { get; set; }
  public string Street { get; set; }
  // ...
}

// Order V2
public class ThirdPartyOrderV2
{
  public decimal Amount { get; set; }
  public ThirdPartyOrderItemV2[] LineItems { get; set; }
  public ThirdPartyOrderAddressV2 BillingAddress { get; set; }
  public ThirdPartyOrderAddressV2 ShippingAddress { get; set; }
  public object SomeV2ThingIDontCareAbout { get; set; }
  // ...
}

public class ThirdPartyOrderItemV2
{
  public int ItemID { get; set; }
  public int Quantity { get; set; }
  // ...
}

public class ThirdPartyOrderAddressV2
{
  public string Name { get; set; }
  public string Street { get; set; }
  // ...
}

// Order V3
public class ThirdPartyOrderV3
{
  public decimal Amount { get; set; }
  public ThirdPartyOrderItemV3[] LineItems { get; set; }
  public ThirdPartyOrderAddressV3 BillingAddress { get; set; }
  public ThirdPartyOrderAddressV3 ShippingAddress { get; set; }
  // ...
}

public class ThirdPartyOrderItemV3
{
  public int ItemID { get; set; }
  public int Quantity { get; set; }
    public string Metadata { get; set; }
  // ...
}

public class ThirdPartyOrderAddressV3
{
  public string Name { get; set; }
  public string Street { get; set; }
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，它们看起来几乎一模一样。出于我的目的，它们*是*相同的，因为我不需要消耗它们之间不同的属性。当我们第一次编写这个项目时，我们没有时间去深入思考它，所以我们(恐怖的恐怖)在解析之后复制并粘贴了我们的逻辑:

```
public class Program
{
    public static void Main()
    {
        var parser = new ThirdPartyOrderParser();
        var order = parser.ParseOrder("text");
        if (order is ThirdPartyOrderV1)
        {
            IngestOrderV1(order as ThirdPartyOrderV1);
        }
        else if (order is ThirdPartyOrderV2)
        {
            IngestOrderV2(order as ThirdPartyOrderV2);
        }
        else if (order is ThirdPartyOrderV3)
        {
            IngestOrderV3(order as ThirdPartyOrderV3);
        }
    }

    private static void IngestOrderV1(ThirdPartyOrderV1 order)
    {
        Console.WriteLine("Order total: " + order.Total);
    }

    private static void IngestOrderV2(ThirdPartyOrderV2 order)
    {
        Console.WriteLine("Order total: " + order.Total);
    }

    private static void IngestOrderV3(ThirdPartyOrderV3 order)
    {
        Console.WriteLine("Order total: " + order.Total);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

——[。网络小提琴](https://dotnetfiddle.net/2ZeGNB)

所有这三个 IngestOrderV*方法对订单的相同属性执行相同的逻辑，但是单独处理每个版本，并且没有共享代码。不理想，不可维护。现在，我们有时间回头看看更好的方法。显然，我们想要做的是这样的事情:

```
public void IngestOrder(string text)
{
  var order = thirdPartyLib.ParseOrder(text);
  var orderTotal = order.Amount;
  var totalQuantity = order.LineItems.Sum(i => i.Quantity);
} 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我很想把这个第三方库的开发者推到公共汽车下，因为他们没有在他们的文档类中使用任何多态性。但是我不能。原因如下:

1.  这个库解析数百种不同的文档类型，所以我确信这些类是由他们的系统自动生成的，这可能需要很长时间才能为一些文档找到一个有效的基类或接口，这些文档从一个版本到另一个版本的变化比我关心的这个文档*的变化还要多。*
2.  我需要用一种通用的方式处理不同的版本。对于他们的所有用户来说，这是不正确的，他们可能经常使用这个库来解析他们公司已经采用的版本，所以对于这个库的大多数用户来说，这不是问题。
3.  声明一个`IThirdPartyOrder`接口听起来不错，只是属性不是简单的值类型。你需要`IThirdPartyOrderItem`和`IThirdPartyOrderAddress`等等。如果像这样用非特定类型替换所有版本对象的所有属性，就会失去 V3Item 和 V3Order 之间的所有绑定。这样，每当开发人员想要引用对象的特定于版本的方面时，他/她就必须将其属性转换为特定的版本。这个类有数百个自定义类型的属性，所以变得非常快。

好了，抱怨(和原谅)到此为止。在这种情况下，这些对象是相同的“形状”，但不共享一个接口，我该怎么办？以下是我思考和/或尝试过的一些想法:

### 用`dynamic`获得创意

如果您将从解析器返回的对象视为`dynamic`，那么您可以访问所有的属性，而无需关心类型绑定。所以，只要属性有相同的名字，我们就可以这样处理:

```
public class Program
{
    public static void Main()
    {
        var parser = new ThirdPartyOrderParser();
        dynamic order = parser.ParseOrder("text");
        Console.WriteLine("Order total: " + order.Total);
        // ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

——[。网络小提琴](https://dotnetfiddle.net/CjFM5k)

这样做很牛逼。我们现在可以处理任何我们遇到的对象，只要它具有我们指定名称的属性。实际上，我们访问对象就像在 JavaScript 领域一样。写一次就可以了，对吧？

嗯，这有一些巨大的不利于 T2 的地方。JavaScript 为我们的对象上不存在的属性返回`undefined`,但是。如果我们试图引用一个不存在的属性，NET 将抛出一个硬异常。所以基本上你是把属性检查推迟到运行时，而不是编译时。您可以通过再次将动态转换为对象来解决这个问题，然后在调用它之前使用反射来检查属性是否存在，但是这真的很繁琐。将类型和属性检查延迟到运行时是非常危险的。它会卸下你枪的保险。

如果我们走这条路，不仅不安全，你会失去所有的智能。重构成为巨大的痛苦，生产中的调试问题成为难题。

最后:不能动态调用扩展方法。所以，也许*你*知道 LineItems 属性总是一个集合，但是编译器不知道。它甚至不知道`LineItems`的存在，更不用说它的类型了。见鬼，它连`order`是什么都不知道。所以，没有一堆造型和神奇的诡计，你不能迭代通过你的属性或调用 LINQ 或其他扩展方法

### 其他“动态”方法

我想到的其他一些想法:这个库声明，由于订单是单向的 POCOs(没有反向引用)，它们都可以很容易地序列化为 JSON、XML、YAML 等。因此，也许我可以将对象序列化为 JSON，并使用 Json.NET job object 遍历方法遍历对象:

```
public class Program
{
    public static void Main()
    {
        var parser = new ThirdPartyOrderParser();
        var order = parser.ParseOrder("text");
        var json = JsonConvert.SerializeObject(order);
        var obj = JObject.Parse(json);
        Console.WriteLine("Order total: " + obj["Total"]);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

——[。网络小提琴](https://dotnetfiddle.net/dqRh24)

就像上面的动态类思想一样，我们不会得到智能感知或编译器类型检查。但是检查属性的存在要容易得多，并且您不需要通过反射来获得创造性。你把 LINQ 带回来。另外，如果我真的想的话，我可以编写一个与第三方订单类具有相同公共结构的类(比如，`ThirdPartyOrderCommon`),并进行强类型反序列化来获得我想要的东西。

但是另一方面，就像我说的，这个 orders 对象很大，包含数百种属性类型。我需要编写和维护(以及复制)大量代码。另外，序列化一个对象只是为了将它从一种类型转换成另一种类型是错误的(也许 [AutoMapper](http://automapper.org/) 是更好的选择？)

### 所以，我被难住了

当然，我不是唯一一个遇到这种事情的人。你们都是做什么的？