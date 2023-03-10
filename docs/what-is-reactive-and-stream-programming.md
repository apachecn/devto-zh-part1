# 什么是反应式和流编程？

> 原文：<https://dev.to/mortoray/what-is-reactive-and-stream-programming>

反应式编程是一种[范式](https://mortoray.com/topics/paradigms/)，它在价值之间创建了永久的关系。反应式表达式不是一次性计算，而是在源值发生变化时更新其结果。也许最初是在电子表格中普及的，通常以音频处理的流形式出现，现在它已经成为用户界面编程和其他反馈系统中的一个有价值的工具。

## 为什么

反应式表达式让我们考虑数据之间的高层关系，而不是处理无休止的数据洗牌。数据被视为活动结构，其输出反映当前状态。

这种范式很好地处理了频繁变化且用户可见的数据。它在数据和接口之间提供了一个清晰的桥梁。它是声明式编程的天然伙伴。

Reactive 也可以被看作是比[事件编程](https://mortoray.com/2017/06/26/what-is-event-programming/)更高级别的范例。有时，当它确实适用时，反应式代码将比等效的事件代码小得多，也干净得多。

> 本文主要关注函数式编程的好处和核心品质。和所有范例一样，都有局限性。我将在以后的文章中讨论这些内容。

## 基础知识

考虑如下的基本表达式:

```
c = a + b 
```

Enter fullscreen mode Exit fullscreen mode

在非反应式范例中，比如函数式或命令式，将只计算一次`c`的值:它是两个输入`a`和`b`的总和。在反应范式中，这反而创造了一种永久的关系。`c`成为了`a`和`b`的忠实追随者，无论它们中的哪一个得到了新值`c`也会得到一个新值。

在电子表格中，这允许创建活动的公式。用户可以更改源值，所有依赖于该值的单元格都会自动更新。用户不担心传播是如何发生的，只关注数据之间的关系。

## 界面中的反馈

反应式表达式现在在用户界面中很常见。它们简化了将数据显示到屏幕上，以及将用户输入返回到数据的过程。

让我们看一个简单的用户购物清单。我将使用简单的声明式 UI 语法，因为它与反应式表达式结合得很好。假设有一个名为`shoppingList`的源列表。

```
<Dialog>
    <Field Title="Number of Items" Value="count(shoppingList)"/>
    <Field Title="Total Cost" Value="sum( pick( shoppingList, 'cost' ) )"/>
    <ForEach Items="shoppingList">
        <Field Title="Name" Value="name"/>
        <Field Title="Cost" Value="cost"/>
        <PrioritySelection Value="priority"/>
    </ForEach>
</Dialog> 
```

Enter fullscreen mode Exit fullscreen mode

如果程序的任何部分向`shoppingList`添加新项目，该显示将自动更新。`count`将更新到新的商品数量，`sum`将增加成本，`ForEach`将在列表中显示新的商品。

现在假设用户输入一个优惠券代码，给他们一个项目的折扣。优惠券处理代码将更新其中一个商品的`cost`，并且将重新计算总费用。

> 实际上，很少有框架提供 100%的反应式数据。相反，数据必须明确标记为“可观察的”或反射状态的一部分。即使在反应式应用程序中，大多数变量和代码也不是反应式的。为了效率和语法方便，有选择性是有意义的。

### 双向

反应式编程的重点是从源到目标的数据转换。这种关系也可能是双向的。例如，上面的`priority`字段可以作为下拉选择呈现给用户。如果他们更新了项目的优先级，这将会反映到源数据中。

这种双向关系只能为简单的表达式提供，比如将 UI 元素直接绑定到值。不是所有的表达都是双向的。用户更新`Number of items`字段没有意义；没有办法逆转`count`关系。

### 功能性的，或不实在的

许多反应式表达看起来很有功能性。这是一种表达数据之间关系的便捷形式。我们看到很多像`count`、`map`和`sum`这样的函数。事实上，许多参考资料将该范例称为“功能性反应式编程”，其中较短的“反应式”术语适用于较低层次的机制。

这并不意味着我们不能在反应式表达式中添加命令式挂钩。通过监听更新事件在反应式和非反应式模块之间架起桥梁的代码并不少见。

```
var pageData = Observable()
page.property.onChanged( function( newProperty ) {
    //TODO: find and reprimand colleage that failed to make a reactive HTTP layer
    var request = HTTP.create( baseUrl + "/user/" + newProprerty.user )
    request.onSuccess( function( data ) {
        pageData.set( data )
    })
    request.onError( function( err ) {
        pageData.failed( err )
    })
    request.get()
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果远程数据也可以自己改变，那么这种桥可能会有点问题。完全的反应式集成需要将范例扩展到服务器——服务器需要将更新推送到客户机。

如果我们想找点乐子，我们也可以引入非静态表达式。我们可能想做一个每 30 秒更新一次的认证令牌:

```
var interval = Timer.seconds(30)
var authToken = interval.map( function(t) {
    return totp.getToken(t)
}) 
```

Enter fullscreen mode Exit fullscreen mode

或者我们可以直接连接到移动设备的传感器:

```
<P>In case of falling consult arrow for up direction.</P>
<Image File="arrow.png">
    <Rotation Degrees="vectorToAngle( accelerometer.up )">
</Image> 
```

Enter fullscreen mode Exit fullscreen mode

这些例子让我们想到反应式编程可能是流处理的一种形式...

## 流处理

音频流处理本质上与反应式编程相同。我们有各种各样的输入，通过效果图连接起来，最终组合成最终的输出流。通过节点的核心数据是音频数据，但是其他类型的数据也是存在的。各种转盘和滑块也馈入这个网络，任何踏板或外部开关也是如此。

> *“本质上”在这里实际上意味着这两个是可证明的相同模型，但我不会在这里做枯燥的理论，我只是挥挥手，希望你接受这个结论。

流程图和术语经常被用来描述反应式方法。一些框架甚至提供了只有在被解释为流时才有意义的操作符。因为流可能是反应模型的最普通的形式，所以总是用这些术语来思考并不是一个坏主意。当然，当用户对文本字段进行一次性更新时，想到流也有点奇怪。

## 在可能的地方使用

当一个反应式模型应用于一个领域时，它通常是最好的模型，远远领先于其他模型。它隐藏了保持数据同步的复杂性。生成的代码易于阅读，并且清楚地显示了数据之间的关系。

反应式编程融合了其他范例。它与函数式编程密切相关，通常被称为“函数式反应式编程”。它是事件编程的自然扩展，并且易于与命令式代码集成。它是声明式编程的优秀伙伴:直接看到数据之间的关系是一种优雅。