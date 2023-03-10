# 为什么我们在生产中使用 fsharp 数据

> 原文：<https://dev.to/cboudereau/why-we-use-fsharp-data-in-prod>

最初发布在[我的博客](https://cboudereau.github.io/fsharp/data/prod/2017/08/18/why-we-use-fsharp-data-in-prod.html)

我目前在一家处理数百个连接(应用程序协议和域适配器+大数据)的公司工作。

自 2014 年以来，我们在生产中逐步使用 fsharp 数据(自 2012 年以来，我在生产中使用 fsharp)。

如今，我们在 prod 中拥有超过 10%的份额和前 3 名中的 1 名(顶级业务、高性能),此外还有一个完整的 fsharp 中的定制大数据解决方案(由于 zmq + 7z + Azure，该解决方案是针对交换数据的销售/缺货风险的公司担保)

我们在项目的不同阶段使用 fsharp 数据:评估、概念验证、开发。

用 fsharp 制作了 3 年，我想连载一下我的经历。

## 我们的语境

连接是一个适配器，用于与合作伙伴交换数据。
这种伙伴经常使用一种“标准”。这个标准 [OTA](http://opentravel.org/#sthash.4w8LbhfH.dpbs) 试图统一两个合作伙伴之间的通信。坏消息是:OTA 里的一切都是可选的。

> 我们 70%的合作伙伴尝试使用 OTA 标准，其他人使用他们自己的协议

有什么问题？

> 协议是冗长的(由于语义问题、间隔范围，...)
> 
> 每个合作伙伴都有自己的一组必需和可选数据。
> 
> 值是在一个区间上定义的(另一篇文章可能会专门讨论这一部分)，每个合作伙伴都有自己的区间值范围
> 
> 一些合作伙伴不再使用 OTA 或者他们不知道 OTA 存在(由于历史原因，...)
> 
> 即使合作伙伴的界面很差，我们也必须尽快将其集成

## 对我们现有应用的代码分析

刚到这家公司的时候，我已经跑了[n 依赖](http://www.ndepend.com/)和[声纳](https://www.sonarqube.org/)去看:

*   模块是如何依赖的(可视化并导航到依赖图)
*   多少行代码依赖于一个模块(使用 Resharper)

每个连接器大约有 35%的代码使用`System.Xml.Linq`，50%的测试专用于序列化/反序列化部分(测试集中在序列化+域自适应，没有分离)。

## 维护性

当我们必须修复一个现有的应用程序时，我们必须添加一个测试，运行测试，看看哪里有问题。这真的很无聊，伴侣经常会破坏他们的和谐。

当我们用 xml linq 构建一个新的连接器时，我们最终会有很多这样的方法:

```
private static Stuff BuildStuff(XDocument responseXml, XElement name) 
```

传递`XElement`、`XDocument`，任何 xml linq 参数都不提供上下文:the what。我们必须不断地将样本放在右边，因为它是完全动态的。

当我分析这类代码时，如果没有样本和全局，就很难分析代码。

下面是一个 xml 数据示例(这不是一个依赖于领域的示例) :

```
<node>

    <item>
        <value>10</value>
    </item>
    <value>100</value>

</node> 
```

获取所有值的关联代码:

```
class Program
{
    private static IEnumerable<decimal> Values(XElement x)
    {
        return x.Descendants("value").Select(y => Decimal.Parse(y.Value));
    }
    static void Main(string[] args)
    {
        var xml = XDocument.Parse(@"<node><item><value>10</value></item><value>100</value></node>");

        var v1 = Values(xml.Root);
        var v2 = Values(xml.Descendants("item").First());

        Console.WriteLine("from root");
        v1.ToList().ForEach(x => Console.WriteLine("value : {0}", x));

        Console.WriteLine("from item");
        v2.ToList().ForEach(x => Console.WriteLine("value : {0}", x));

        Console.ReadLine();
    }
} 
```

当数据越大，这种方法的数量也就越多，你必须分析方法的用法来理解上下文。

在前面的示例中，函数不会根据上下文返回相同的值(因为它强制使用元数据和 XElement，而不是使用实际数据)。

> 注意:我尝试使用 xsd.exe 来生成相同的 C#代码，但是有一些奇怪的伙伴 xml 不能正确地与 xsd 一起工作。当它工作时，生成的代码真的很难更新。
> 
> 在 99%的情况下，我们的合作伙伴不提供 xsd。这个解决方案只适用于 xml，不适用于 json

下面是一个带有 XmlProvider 的版本 [Fsharp。数据](http://fsharp.github.io/FSharp.Data/):

```
#r @"..\packages\FSharp.Data\lib\net40\FSharp.Data.dll"
#r "System.Xml.Linq"

open FSharp.Data

let [<Literal>] nodeSample = """<node>

    <item>
        <value>10</value>
    </item>
    <value>100</value>

</node>"""

type Node = XmlProvider< nodeSample >

let node = Node.Parse(nodeSample)

printfn "from root"
printfn "%i" (node.Value)
printfn "from item"
printfn "%i" (node.Item.Value) 
```

> 在这个版本中，不需要构建一个函数来提取和解析值。
> 项目和根值之间没有明确的上下文

## 估计的朋友

在我们的团队中，我们使用 t 恤尺码表来分析分派工作的复杂性。
我使用 fsharp 用我们的领域、合作伙伴的数据样本构建了一个简单的 fsx，以便了解他们的领域(不仅是协议，还有随着时间间隔进行的领域和业务调整)。

FSharp。数据加速了这个过程，因为瓷器和管道部分(xml，json，...所有服务层)由 FSharp 完成。数据，我们有一个更好的复杂性反馈。原因是有时合作伙伴协议非常冗长，在交互模式下导航到 json 或 xml 比导航到实例化对象更困难。

在使用 fsharp.data 之前，我是一个系统。Xml.Linq ninja:)，但现在，每当有冗长/复杂的数据需要理解时，我首先使用 fsharp 脚本。

## 灵活性

当合作伙伴改变他的协议时，我们必须用新的样本来改变适配器，并且很难区分具有完全不同数据的两个样本。因此，我们添加了一个新的测试，但由于上下文问题，很难看出是否有相同的名称，但结构和范围不同。

> 如果所有的测试都完成了，那么只有一个测试是针对新协议的，其他的都是基于旧协议
> 的。如果存在基数问题，就很难看出它是如何影响业务部分的(例如价格范围)。

我更喜欢使用 fsharp，因为如果有一个新的作用域(比如一个新的节点)，代码将无法编译，我们必须对整个代码进行推理。

> 如何在同一个 app 中同时支持新旧协议而不出错？
> 
> 我们需要一个工具来用新协议挑战现有代码

以下是 fsharp 数据帮助我们提高流程质量和速度的一些例子:

比如 Xml Linq，FSharp。数据公差:

*   我们不需要的变化呢:

```
#r @"..\packages\FSharp.Data\lib\net40\FSharp.Data.dll"
#r "System.Xml.Linq"

open FSharp.Data

type Node = XmlProvider< """<root><code>ABC</code><id>50</id></root>""" >

let node = Node.Parse("""<root><code>ABC</code></root>""") 
```

> 请注意，提供的 xml to parse 方法没有 id 元素(运行时和设计时是不同的)

交互式显示器:

```
Code : ABC 
```

*   在运行时中断变更(当合作伙伴在产品中直接中断时:数据在运行时与规范不同)

```
#r @"..\packages\FSharp.Data\lib\net40\FSharp.Data.dll"
#r "System.Xml.Linq"

open FSharp.Data

type Node = XmlProvider< """<root><code>ABC</code><id>50</id></root>""" >

let node = Node.Parse("""<root><code>ABC</code></root>""")

printfn "Id : %i" (node.Id) 
```

代码可以编译，但在运行时出现了一个问题:

```
System.Exception: XML mismatch: Expected exactly one 'id' child, got 0 
```

> 对于这种情况，您应该考虑用相关数据记录该错误。

*   对遗留代码进行重大更改(快速修复) :

```
#r @"..\packages\FSharp.Data\lib\net40\FSharp.Data.dll"
#r "System.Xml.Linq"

open FSharp.Data

type Node = XmlProvider< """<root><code>ABC</code></root>""" >

let node = Node.Parse("""<root><code>ABC</code></root>""")

printfn "Id : %i" (node.Id) 
```

> 请注意，所需的 Id(由 printfn 使用)已经从给定的样本中删除

在设计时有一个错误，我们必须在不添加新的单元测试的情况下修复它(如果您想同时支持测试和示例，我们可能必须在不涉及测试和示例的情况下修改代码)。

*   当您必须支持多个版本时

```
#r @"..\packages\FSharp.Data\lib\net40\FSharp.Data.dll"
#r "System.Xml.Linq"
open FSharp.Data

type Node = XmlProvider< """
<samples>
<!-- version 1 -->
<root><code>ABC</code></root>
<!-- version 2 -->
<root><productcode>ABC</productcode></root>

</samples>""", SampleIsList=true >

let toResult error = function
    | Some x -> Ok x
    | None -> Error error

let (<|>) x y = 
    match x with
    | Ok x' -> Ok x'
    | Error e1 ->
        match y with 
        | Ok y' -> Ok y'
        | Error e2 -> sprintf "%s\r\nor%s" e1 e2 |> Error

let prettyprint (node:Node.Root) = 
    (node.Code |> toResult "expected code element") <|> (node.Productcode |> toResult "expected Productcode element")
    |> printfn "Code : %A"

let nodeLegacy = Node.Parse("""<root><code>ABC</code></root>""")
let nodeNew = Node.Parse("""<root><productcode>ABC</productcode></root>""")

prettyprint nodeLegacy
prettyprint nodeNew 
```

> 我在 XmlProvider 的示例中添加了两个版本，方法是添加一个新的根节点和 SampleIsList to true 参数。在这种情况下，XmlProvider 跳过第一个元素，并考虑要处理许多子元素。
> 
> 我添加了两个函数:当元素不存在时添加错误消息的 toResult 和处理两个版本的 orElse `(<|>)`函数。prettyprint 函数显示结果

## 表现

FSharp。数据使用擦除类型提供程序，因此这种抽象的成本是在设计时支付的。

构建应用程序时，所有提供的类型(如我们示例中的类型节点)都将被丢弃。

所以所有的抽象都被系统内联了。Xml.Linq 调用。性能相当于系统。Linq 版本。

我们最冗长的应用程序使用 fsharp.data 交换，接近 3 . 000 . 000 rq/天。

## 结论

*   我们首先在现有的 csharp 应用程序上使用 fsharp 模块，以便推断合作伙伴域
*   性能相当于手工制作的 Xml Linq 版本
*   估算部分更快，因为它需要更少的 xml linq 管道
*   由于从单元测试反馈转移到设计时和编译器反馈，快速修复是可能的
*   围绕我们合作伙伴的领域进行了更多的测试，而不仅仅是序列化/反序列化部分
*   我已经用 fsharp 脚本替换了我的 linqpad + fiddler
*   当突破性变化太高时，我们使用 fsharp 完全重写适配器，并看到 LOC 确实降低了(接近 40%)
*   概念证明代码可以重用(比提取 linqpad 代码并在单元测试后导入到现有的+ cover 代码构建中要好)
*   当您只处理 fsharp 时，语言 ML 语法提供了一种避免 NRE(空引用异常)更好的方法。当对象来自 csharp 时，存在 Option.ofObj 适配器。
*   我们已经开始了另一种体验，并对以完整 fsharp [Outatime](https://github.com/cboudereau/Outatime) 值表示的区间进行了调整