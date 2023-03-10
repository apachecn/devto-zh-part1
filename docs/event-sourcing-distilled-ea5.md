# 事件源提取

> 原文：<https://dev.to/byronruth/event-sourcing-distilled-ea5>

*TL；博士*。活动采购是这样的...

```
f1(state, command) -> events
f2(state, events) -> state    // the golden function 
```

而不是这个..

```
f(state, command) -> state 
```

* * *

由于最近的趋势主题，包括领域驱动设计(DDD)、微服务、分布式日志等，事件源(ES)的受欢迎程度稳步上升。有许多文章从这些主题的角度解释了 es，但是我觉得有必要从不同的角度来解释它，与任何特定的技术选择或环境无关。

从根本上说，ES 是表示状态的另一种方式。就是这样。

花点时间想想你熟悉的任何系统。写下你可以执行的一两个命令(动作)以及你观察到的该命令的效果。从用户的角度来看，这是有意义的。我有一个*意图*，我发送一个命令来表达我的意图，然后我观察这个命令对状态的影响。

这种抽象可以表示为一个函数，它接受系统的当前状态和一个命令，并返回系统的新状态(为了简洁起见，省略了错误或拒绝)。

```
f(state, command) -> state 
```

我现在要观察的是新的状态。*如何*观察状态取决于 API 提供的内容(对象列表、id 对象等)。

ES 不是从一个状态到另一个状态，而是将这个状态转换分解成两个函数。第一个函数采用相同的参数、先前的状态和命令，但现在返回一个或多个事件。

```
f1(state, command) -> events 
```

回忆命令表达了要执行的动作的*意图*。这些事件也捕捉到了变化的意图，但是也表达了*命令导致了什么*变化。事件是过去式，因此*是不可改变的事实*。

转换的第二部分是获取当前状态并应用事件以产生新状态。

```
f2(state, events) -> state 
```

仅将新事件应用于当前状态对于*在线*命令处理是常见的。但是，如果某些离线进程需要，可以使用此函数在任何时间点重新创建状态。这是通过传递到某个时间点的 *null* 状态和事件的完整历史来完成的。

这为什么有用？将事件如何产生(通过命令)与事件如何应用于状态解耦，使得能够改变状态的实现或事件最终如何应用。因为事件捕获了意图和改变了什么，它们*是状态的*真相的来源。状态只是一个结构，表达了这些事件在某个时间点的顶点。

使事件成为一流事件提供了额外的优势，例如支持出于操作或审计原因的检查，并使向系统中其他感兴趣的组件广播事件变得容易，以便进行实时事件驱动的更新。