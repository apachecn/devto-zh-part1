# 基于消息的 API，第 2 部分

> 原文：<https://dev.to/kspeakman/message-based-api-part-2-67c>

在[之前的文章](https://dev.to/kspeakman/message-based-apis)中，我描述了一个基于消息的 API，其中与 API 的通信是通过发布消息来完成的。这是 REST 的替代方案，尽管我的实现与 REST 有许多共同的特征。可能需要替代方案的原因仅仅是为了改变思维。REST 描述了许多有趣的好处，但是在实践中，它经常将开发人员引向将 URL 与数据库实体一一对应的道路。客户机和 API 内部之间的这种紧密耦合使得系统变得脆弱，难以改变。然而，从消息的角度思考允许客户从 API 交互的目标和意图的角度思考，而不必知道墙后面是什么。

在这篇文章中，我将进一步推进面向消息的思想。一直到如何表示 API 内部的变化。首先，让我们回顾一下在 API 中“修改东西”的常见方式，比如将数据保存到数据库中。

```
 // validate, decide, setup data for saving
    var data = ... ;
    ...
    Sql.Write("INSERT ...", data); 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果您正在使用 ORM(我希望您不要这样做)。

```
 // validate, decide, setup data for saving
    var data = ... ;
    ...
    ormContext.Add(data); 
```

Enter fullscreen mode Exit fullscreen mode

像这样的代码**混合了关注点**。即使在这里使用类似于存储库模式的东西，您的业务逻辑也是在制定决策和执行这些决策的副作用。然后在这里添加更多的副作用就变得非常容易了。比如:现在用户希望每当这种事情发生时都能收到邮件。因此，dev 添加了一行来发送电子邮件。

```
 // validate, decide, setup data for saving
    var data = ... ;
    ...
    Sql.Write("INSERT ...", data);
    emailer.Send(data); 
```

Enter fullscreen mode Exit fullscreen mode

这里的错觉是电子邮件功能被封装在其他地方，我只是在这里调用它。但事实是，如果电子邮件功能抛出异常，您的业务逻辑也会抛出异常。如果这是不可接受的，那么现在您的业务逻辑中充满了 try/catch，以处理产生副作用的问题。我们也在测试中为自己创造了更多的工作。我们必须模拟 SQL 和 emailer 对象，以便能够测试决策逻辑。

## 信息能有什么帮助？

如果我们可以只返回做出的决定(作为一条消息),并让其他组件对应该产生的副作用负责，会怎么样？也许业务逻辑会是这样的。

```
 // validate, decide
    ...
    // return message
    return new OrderPlaced { ... }; 
```

Enter fullscreen mode Exit fullscreen mode

与前面的例子相比，这段代码测试起来有多难？这段代码有多难理解？我相信这两点都很容易。没什么好嘲笑的。没有什么外部的需要担心。

> 为了将这些消息与用于与 API 通信的消息区分开来，它们通常被称为不同的名称。API 通信消息通常被称为*命令*(与 [GoF 模式](https://en.wikipedia.org/wiki/Command_pattern)混淆不清)，代表 API 内部决策和其他事件的消息被称为*事件*。

一旦代码做出决定，然后返回一个表示该决定的事件，那么 API 就将该事件传递给任何感兴趣的组件(通常称为事件处理程序)。某个相关方可能会将该事件转换成 SQL 语句。另一个人可能会把它转换成电子邮件。

我听到你说“等等，等等，等等。这和事件采购不一样吗？”其实不是。使用事件来表示 API 中的变化并不要求您保存和加载事件作为事实的来源。您可以非常愉快地将关系数据库作为事实的来源，并简单地将 API 事件转换成 SQL 语句来更新事实的来源。我有做这个的 API。使用内部事件允许您将决策代码与效果代码分开，而不考虑事件来源。

> 要做到这一点，事件源不是必需的。但是它确实将这种能力提升到了一个新的水平。因为有了事件源，你的事件可以被其他计算机上运行的服务看到，而不仅仅是 API 内部的服务。然而，我在棕地项目中使用这种没有事件源的模式，在棕地项目中，将现有数据迁移到事件中成本太高。

这也提供了一个挂钩点，以在业务级别而不是数据级别响应感兴趣的事件**。如果没有事件，当数据被写入关系表时，它就失去了所有的业务语义——它现在只是数据。当然，我可以添加一个更新时间戳来查看它是何时更改的。或者我甚至可以记录以前的状态，这样我就可以区分当前和以前的版本。这告诉我数据的哪些部分发生了变化，但从业务的角度来看，它可能仍然不能告诉我*发生了什么*。我只能猜测了。**

不过，对于事件，我可以根据事件对业务的意义来思考。以及我的组件需要如何响应该事件(如果有的话)。以不影响生成事件的业务逻辑的方式添加案例也很容易。

```
public class EmailHandler() {

    private Email GenerateNewOrderEmail(OrderPlaced e) { ... }
    private void SendEmail(Email email) { ... }

    public void Handle(IApiEvent apiEvent) {
        switch (apiEvent) {
            case OrderPlaced e:
                SendEmail(GenerateNewOrderEmail(e));
            default:
                return; // do nothing

            // maybe later OrderShipped case is added
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 为了使事件有用，它们应该用业务术语建模，而不是数据术语。例如，`OrderUpdated`是没有业务意义的数据。只是说订单中的一些数据被更新了。不去挖掘它的数据，我无法判断我是否关心这个事件。另一方面，`OrderCanceled`或`OrderPaidInFull`可能是建模良好的业务事件，因为它们反映了订购流程的语义。

## 现实世界

因此，现实世界带来了进一步的改进，这是我上面的简单例子所没有涵盖的。首先，我们需要能够返回多个事件。

```
 // validate, decide, return decision
    ...
    return new [] {
        new ItemTransferredToFulfillment { ... },
        new ItemSoldOut { ... }
    }; 
```

Enter fullscreen mode Exit fullscreen mode

经常有批处理用例，我们需要以全有或全无的方式运行多个单独的命令。因此，业务逻辑不是直接返回事件，而是将它们添加到一个“待定”列表中。您可能认为这是工作单元的模式。

```
 // validate, decide, return decision
    ...
    context.AddPending(
        new ItemTransferredToFulfillment { ... },
        new ItemSoldOut { ... }
    ); 
```

Enter fullscreen mode Exit fullscreen mode

为了返回多种消息类型，就好像它们是相同的类型一样，通常由标记接口来完成这项工作。在函数式语言中，可能会使用联合类型。

```
// marker interface, no properties or methods
public interface IApiEvent { }

// elsewhere...

public class ItemSoldOut : IApiEvent
{ ... }

public class ItemTransferredToFulfillment : IApiEvent
{ ... } 
```

Enter fullscreen mode Exit fullscreen mode

取决于你的语言有什么样的模式匹配工具，当给定标记接口时，处理事件可能是令人讨厌的。用 C# 7(或者 F#)也没那么差。

```
public void Handle(IApiEvent apiEvent) {
    switch (apiEvent) {
        // only care about this case and no others
        case ItemSoldOut e:
            ...

        default:
            return;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我经常希望在一个事务中批量处理多个更新，这样要么对系统进行所有更改，要么什么都不做。所以我首先生成“补丁”，然后在一个事务中运行所有补丁。

```
// part of a class that handles Order table changes
public IEnumerable<Patch> GetPatches(IApiEvent apiEvent) {
    switch (apiEvent) {
        case OrderPlaced e:
            yield return new Patch(
                // query
                "INSERT ... VALUES (@OrderDate, ...)",
                // key/value tuple, list as many as you want
                ("OrderDate", e.OrderDate),
                ...
            );

            // each event can generate multiple "patches"

        ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

根据系统的不同，我可能会将它们全部批处理到一个大型语句中(所有更新都需要一次往返)，或者用代码启动事务并单独运行它们。

### 身份

如果您使用自动递增的 id，那么您可能会遇到一点先有鸡还是先有蛋的问题。因为您的事件处理程序可能需要自动生成的 ID，但是您不知道在创建事件时它会是什么。处理这种情况需要稍微不同的安排，其中持久性不仅仅是“另一个事件处理程序”。您可能必须首先专门运行持久性处理程序，取回自动生成的 ID，然后更新事件以包含它。(这在某些圈子里叫事件浓缩。)

总的来说，我不喜欢使用自动标识作为主要的标识符。因为自动标识是副作用(增加计数器)之上的副作用(插入记录)。从架构上来说，这使得它们[很难处理](https://softwareengineering.stackexchange.com/questions/301620/why-do-some-prominent-web-sites-use-alphanumeric-strings-for-resource-ids-instea/301641#301641)。相反，我将使用 UUID 作为主要身份。然后，如果业务需要一个更友好的标识符，我将使用自动 ID 或用户输入的字符串或其他任何东西作为辅助 ID。但是这个二级标识符将纯粹用于人类友好的搜索。

### 命令处理程序

我也没有真正讨论过命令处理程序。这是我倾向于准备运行业务逻辑所需的所有数据的地方。这样，业务逻辑可以是纯粹确定性的，并且非常容易测试。命令处理程序集成(从数据库加载、调用外部 API 等)与集成测试一起进行。

这里有一个`PlaceOrder`命令的处理程序示例，其中 IO 与业务逻辑交错(`OrderFactory`和`order`调用)，但是业务逻辑仍然是确定性的。

```
public void Handle(ApiContext context, PlaceOrder command) {
    // is this order even valid?
    var order = OrderFactory.Create(command);
    if (order.IsValid) {
        // load inventory status from DB for requested items
        var inventoryStatusList = ... ;
        // maybe it throws for errors, like OutOfStockException?
        order.CheckInventory(inventoryStatusList);
        // get decisions made about this order
        context.AddPending(order.GetEvents());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 实现方式各异

以上只是基于消息的(内部)API 的大概样子。在实践中，一些实现的部分将取决于您正在解决的问题。我发现这种基础设施最令人高兴的一点是，像所有好的代码一样，当你学习新的信息时，很容易改变。

上面的 C#代码是现成的，不保证能编译。我实际上是用 F#写的这种 API，习惯用法略有不同，但是你可以在上面看到它在 OO 语言中同样可以表达。我觉得上面的解释还不够完整，但希望这是一个好的开始。我可能会根据我的想法修改这篇文章。