# Akka 中可重用的响应收集器

> 原文：<https://dev.to/lepovirta/reusable-response-collectors-in-akka-hig>

Akka 中推荐的实践之一是使用[响应收集器模式](http://techblog.net-a-porter.com/2013/12/ask-tell-and-per-request-actors/)而不是[询问模式](http://doc.akka.io/docs/akka/snapshot/scala/actors.html#Ask__Send-And-Receive-Future)进行请求-响应风格的参与者通信。它通过将大部分响应管理置于一个专门的参与者之下，简化了参与者的沟通。参与者系统中没有添加超时和错误处理逻辑，而是使用标准的 tell 机制简单地响应任何查询，而响应收集器参与者决定何时收到足够的响应。

创建一个特别的响应收集器很容易，但是在很多情况下，能够重用现有的 actor 代码会更好。

在本文中，我将展示一个可重用的响应收集器 actor。首先，我将介绍一些在许多响应收集器中可以看到的常见模式，并为这些模式形成一个 API。之后，我将展示使用 API 的响应收集器 actor 的实现。最后，我将演示几个使用 actor 的例子。

## 响应收集器中的常见模式

响应收集器的行为可以总结如下:收集相关的响应，直到收集完所有的结果或者超时，然后将结果提供给消费者。那句话里有不少要求。看看能否从中提取出个别模式。

### 收集相关响应

Akka actors 的一个警告是，Akka 在处理传入消息时不强制类型安全。具体来说，每个消息处理程序必须能够处理任何类型的传入消息。有一些将类型安全引入 Akka actors 的尝试(参见[类型 Actors](http://doc.akka.io/docs/akka/2.4.1/scala/typed-actors.html) 和 [Akka 类型 Actors](http://doc.akka.io/docs/akka/2.4.1/scala/typed.html#typed-scala))，但是为了这个练习的目的，我们将只收集有趣的消息，并记录所有意外消息的错误。

Scala 的`PartialFunction`对于匹配部分输入很有用。用户可以创建一个类型为`PartialFunction[Any, T]`的匹配器，其中类型参数`T`是预期输出的类型。例如，下面的匹配器可以用于只提取输入的字符串:

```
val matcher: PartialFunction[Any, String] = {
  case s: String => s
} 
```

Enter fullscreen mode Exit fullscreen mode

### 确定完成

响应收集器如何决定何时收集了足够多的响应？这取决于使用情况。有时我们对收集某一组回答感兴趣，有时我们只对固定数量的回答感兴趣。让我们创建一个跟踪响应的特征:

```
trait ResponseTracker[T] {
  def addResponse(response: T): ResponseTracker[T]
  def isDone: Boolean
} 
```

Enter fullscreen mode Exit fullscreen mode

特征`ResponseTracker`对收集的响应的状态进行建模。`addResponse`方法用于推进跟踪，而`isDone`用于检测何时收集到足够的响应。让我们来看看这是怎么回事:

```
object Countdown {
  def apply(expectedMessagesCount: Int): Countdown = new Countdown(expectedMessagesCount)
}

class Countdown(expectedMessagesCount: Int) extends ResponseTracker[Any] {
  require(expectedMessagesCount >= 0)

  def isDone: Boolean = expectedMessagesCount == 0

  def addResponse(response: Any): Countdown =
    new Countdown((expectedMessagesCount - 1) max 0)
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的`Countdown`用于跟踪收到的响应数量。跟踪器用预期消息的数量初始化。当收到预期数量的消息时，跟踪器将报告完成。跟踪响应的数量可用于获得响应的粗略样本。这里还有一个例子:

```
object MatchIds {
  def apply[Msg, Id](expectedIds: Set[Id], toId: Msg => Id): MatchIds[Msg, Id] =
    new MatchIds(expectedIds, toId)
}

class MatchIds[Msg, Id](expectedIds: Set[Id], toId: Msg => Id)
  extends ResponseTracker[Msg] {

  def isDone: Boolean = expectedIds.isEmpty

  def addResponse(response: Msg): MatchIds[Msg, Id] =
    new MatchIds(expectedIds - toId(response), toId)
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的`MatchIds`用于跟踪某些响应中的 id。跟踪器用一组期望的 ID 和用于从输入响应中提取 ID 的函数来初始化。当收到响应时，响应的 ID 将从预期的 ID 中删除。当不再有期望的 id 时，跟踪器将报告完成。

### 传递结果并超时

可以使用参与者消息将收集的结果提供给参与者，但是对于不从参与者邮箱读取消息的代码，需要另一种交付机制。

使用`Future`值可以获得收集的结果。未来值代表尚未完成的值的占位符。在我们的例子中，该值是所有收集结果的序列:`Future[Iterable[T]]`，其中`T`是预期结果的类型。或者，如果系统中有错误，未来可以异常完成。

有时，一切都没有按计划进行，一些响应永远不会到达响应收集器。有一个后备计划是很好的:在一定时间过去后停止等待响应。可以使用 Scala 的`FiniteDuration`将超时提供给响应收集器。

将超时事件传达给结果的消费者也是一个好主意。这可以通过用超时错误完成结果来实现。然而，能够检查在超时到达之前收集了什么值可能是有用的。因此，在类型级别表示部分结果是一个好主意:

```
sealed trait ResultState
case object Full extends ResultState
case object Partial extends ResultState

case class Result[T](values: Iterable[T], state: ResultState) 
```

Enter fullscreen mode Exit fullscreen mode

类型`Result[T]`包含所有接收到的响应的集合和一个值，该值表明是否接收到了所有的响应。类型参数`T`是预期结果的类型。

## 响应收集器实现

使用上面描述的 API，我们可以创建响应收集器的实现。我们首先介绍一位新演员，`ResponseCollector` :

```
import akka.actor._
import scala.concurrent.duration._
import scala.concurrent.Promise

class ResponseCollector[T](
  timeout: FiniteDuration,
  initialTracker: ResponseTracker[T],
  result: Promise[Result[T]],
  matcher: PartialFunction[Any, T])
  extends Actor with ActorLogging {

  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

`ResponseCollector`有一个类型参数`T`,表示收集的响应的类型。演员初始化为:

*   执行元停止监听响应的超时持续时间。
*   用于检测收集何时完成的初始跟踪器。
*   可以用收集的结果实现一次(`Promise`)的值。`Promise`可以作为`Future`提供给消费者。
*   一个匹配器，用于从传入的消息中只选择感兴趣的响应。

在类内部，超时事件被设置为在给定的超时持续时间过去后触发。这是通过安排向同一个参与者发送消息来实现的。来自包`akka.actor`的`ReceiveTimeout`消息被用作超时消息。

```
import context.dispatcher

private val scheduledTimeout = context.system.scheduler.scheduleOnce(
  timeout, self, ReceiveTimeout
) 
```

Enter fullscreen mode Exit fullscreen mode

参与者有一个自定义的消息处理程序，该处理程序有状态。状态包含接收到的响应的集合，以及响应跟踪器的当前状态。

在这个处理程序中，只选择与`matcher`匹配的消息。匹配时，响应被附加到已经收集的响应集合中。处理程序还推进响应跟踪器，并检查是否收集了足够的响应。如果追踪器报告已经完成，那么收集到的结果将使用 actor 构造函数中传递的`Promise`报告给消费者，actor 将自行停止。如果还有更多的响应需要收集，actor 将替换它的消息处理程序，以包含更新的响应和响应跟踪器。

```
private def ready(responses: Vector[T], tracker: ResponseTracker[T]): Receive = {
  case m if matcher.isDefinedAt(m) =>
    val response = matcher(m)
    val nextResponses = responses :+ response
    val nextTracker = tracker.addResponse(response)

    if (nextTracker.isDone) {
      log.info("All responses received.")
      result.success(Result(nextResponses, Full))
      context.stop(self)
    } else {
      context.become(ready(nextResponses, nextTracker))
    }

  case ReceiveTimeout =>
    log.warning("Response collection timed out")
    result.success(Result(responses, Partial))
    context.stop(self)

  case m =>
    log.warning("Unknown message: {}", m)
} 
```

Enter fullscreen mode Exit fullscreen mode

除了处理传入的响应，处理程序还处理超时事件。当 actor 接收到`ReceiveTimeout`时，它会将收集到的结果作为部分结果报告给消费者，并关闭 actor。

最后，使用自定义消息处理程序设置参与者，初始状态作为参与者的第一个消息处理程序。

```
def receive: Receive = ready(Vector.empty, initialTracker) 
```

Enter fullscreen mode Exit fullscreen mode

除了 actor 之外，我们还可以为创建 actor 的实例提供有用的函数。`props`函数用于为演员创建`Props`食谱。`apply`函数创建响应收集器，并只将收集的结果作为`Future`和参与者的引用公开。在这两种情况下，超时都是作为隐式参数传递的，传递方式与使用 ask 模式时相似。对于`apply`,我们还隐式地传递了一个`ActorRefFactory`,例如，它可以是一个演员系统或演员的上下文。

```
 import akka.util.Timeout

    object ResponseCollector {
      def props[T](
        tracker: ResponseTracker[T],
        result: Promise[Result[T]],
        matcher: PartialFunction[Any, T])
        (implicit timeout: Timeout): Props =
          Props(new ResponseCollector(timeout.duration, tracker, result, matcher))

      def apply[T](tracker: ResponseTracker[T], matcher: PartialFunction[Any, T])
                  (implicit timeout: Timeout, factory: ActorRefFactory) = {
        val result = Promise[Result[T]]()
        val ref = factory.actorOf(props(tracker, result, matcher))
        (result.future, ref)
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

## 举例

让我们创建一个小例子来演示`ResponseCollector`的用法。在这个场景中，我们有一些分布在参与者之间的数据。为了使这个例子简单，我们的数据集由内存中的散列图表示。我们的`DataStore`演员将地图上的数据提供给任何通过键请求数据的人:

```
case class Data(id: String, contents: String) {
  override def toString: String = s"[$id: $contents]"
}

object DataStore {
  def props(id: String, items: Map[Int, String]) = Props(new DataStore(id, items))
}

class DataStore(id: String, items: Map[Int, String]) extends Actor {
  def receive: Receive = {
    case i: Int => items.get(i).foreach(v => sender() ! Data(id, v))
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`DataStore`还发送一个特定于商店的 ID 以及来自地图的数据。ID 可用于识别数据的来源。`DataStore`也将忽略任何在映射中没有找到键的请求。

对于这个例子，我们将建立三个不同的数据存储。除了最后一个以外，所有的商店都包含键 1-3 的一些信息。

```
implicit val system = ActorSystem()

val allStores = List(
  system.actorOf(DataStore.props(
    "name",
    Map(1 -> "Mike", 2 -> "Robert", 3 -> "Joe")
  )),
  system.actorOf(DataStore.props(
    "location",
    Map(1 -> "UK", 2 -> "Sweden", 3 -> "Germany")
  )),
  system.actorOf(DataStore.props(
    "lastPurchase",
    Map(1 -> "couch", 2 -> "laptop")
  ))
) 
```

Enter fullscreen mode Exit fullscreen mode

对于每个键，我们将设置一个响应收集器，从每个数据存储中收集数据。收集者只对`Data`对象感兴趣，所以我们会相应地调整他们的匹配器:

```
val matcher: PartialFunction[Any, Data] = { case d: Data => d } 
```

Enter fullscreen mode Exit fullscreen mode

为了知道收集器何时可以停止收集，我们将使用`MatchIds`和来自传入数据的 id 来跟踪哪些数据存储已经响应。

```
def getId(e: Data): String = e.id
val tracker = MatchIds(Set("name", "location", "lastPurchase"), getId) 
```

Enter fullscreen mode Exit fullscreen mode

使用匹配器和跟踪器，我们现在可以设置响应收集器。结果，我们收到收集器结果的一个`Future`值和对收集器的 actor 引用。我们将把超时作为隐式参数传递给收集器。

```
implicit val timeout = Timeout(1.second)
val (result1, collector1) = ResponseCollector(tracker, matcher)
val (result2, collector2) = ResponseCollector(tracker, matcher)
val (result3, collector3) = ResponseCollector(tracker, matcher) 
```

Enter fullscreen mode Exit fullscreen mode

当从数据存储中请求数据时，我们可以使用收集器 actor 引用作为发送方，以便将响应发送给收集器。

```
allStores.foreach { store =>
  store.tell(1, collector1)
  store.tell(2, collector2)
  store.tell(3, collector3)
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将使用之前收到的`Future`值向用户提供结果。对于这个例子，我们将只打印结果:

```
def printResult(r: Result[Data]): Unit = {
  val status = r.state match {
    case Full => "All values received"
    case Partial => "Only some values received"
  }
  val values = r.values.mkString(", ")
  println(s"$status: $values")
}

implicit val ec = system.dispatcher

result1.foreach(printResult)
result2.foreach(printResult)
result3.foreach(printResult) 
```

Enter fullscreen mode Exit fullscreen mode

运行这个示例将打印出所有三个收集器的结果。前两个结果将打印出完整的结果。由于我们没有为第三个键指定`lastPurchase`信息，第三个收集器将在一秒钟后超时，并且只打印出部分结果。

```
All values received: [lastPurchase: laptop], [name: Robert], [location: Sweden]
All values received: [lastPurchase: couch], [name: Mike], [location: UK]
Only some values received: [name: Joe], [location: Germany] 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在本文中，我演示了如何实现一个可重用的响应收集器 actor。该解决方案允许自定义参与者如何确定何时收到足够的响应，如何确定哪些响应是相关的，以及在超时和交付部分结果之前等待响应的时间。我还展示了一个如何使用 actor 及其行为的示例。

演员仍有定制和改进的空间。例如，可以将超时机制定制为在每个收集的响应之后重置，可以添加错误处理，并且可以将响应作为流而不是批处理来交付。

我已经在 Github Gist 中提供了代码。您可以随意扩展代码。

感谢你的阅读，祝你快乐！