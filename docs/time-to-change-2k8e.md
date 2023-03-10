# 是时候改变了；在 OpenEdge 中使用计时器 OCX

> 原文：<https://dev.to/patricktingen/time-to-change-2k8e>

DataDigger 是一款面向 OpenEdge 开发者的开源数据库浏览器。它使他们能够查看、更新、删除、即时通讯和导出数据库中的数据。DataDigger 完全是用 OpenEdge 4GL 编写的，有超过 40，000 行代码。里面是一些真正的宝藏，所以我将剖析数据挖掘者，揭示他们。今天:使用计时器来改善用户界面。

当你改变文件时，你曾经仔细观察过 DataDigger 做了什么吗？如果没有，我想邀请你这样做。就现在。去吧，我等着。启动 DataDigger，将焦点放在表列表上，并按一次光标向下键。在右侧，您将看到所选表格的字段:

[![ablvtco](img/4ebfa42bab4be7f7165b31ecb24ab854.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3xGWmNNy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datadigger.files.wordpress.com/2018/01/ablvtco.png%3Fw%3D840)

这里没有消息。再次并立即按下光标向下键。再一次。注意浏览字段发生的情况。你注意到了吗？如果您足够快，浏览字段不会更新。它跳过了，因为它知道你无论如何也不打算看它。

#### 它是怎么知道的？

亲爱的，这很简单，因为你没有在表名上停留足够长的时间来显示字段。显示字段时会有一点延迟。我会告诉你怎么做。

我创建了一个可以从 GitHub 下载的小演示来展示它是如何完成的。如果您下载它并对经典运动数据库运行 wCustPerSalesrep1.w，您将会看到如下内容:

[![snvh001](img/01ac32b9c15e98d56822f1fc4aa8bac7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--_oGo1mtp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://datadigger.files.wordpress.com/2018/01/snvh001.png%3Fw%3D840)

这是一个非常简单的实现，但是浏览一下销售代表，您会立刻看到相关的客户。现在想象一下，收集子记录(在本例中是客户)可能需要一些时间。浏览会发生什么变化？假设您想使用光标键从销售代表 BBB 转到 GPE。这意味着要按三次键。但是对于中间的每个销售代表，我们需要收集数据，这是浪费时间。为了说明这一点，只需取消左侧浏览的值更改触发器中的一些代码的注释，使其看起来像这样:

```
ON VALUE-CHANGED OF brSalesrep IN FRAME DEFAULT-FRAME
DO:
  ETIME(YES).
  DO WHILE ETIME < 500: END.
  {&OPEN-QUERY-brCustomer}
END. 
```

Enter fullscreen mode Exit fullscreen mode

这将模拟运行收集客户的代码需要 haf 一秒钟来完成。谁知道呢？它可能通过相对较慢的连接运行在 appserver 上。现在再运行一次。注意到恼人的延迟了吗？是时候解决了。

#### 介绍计时器

想法如下:如果用户选择了另一个销售代表，然后等待几毫秒，看看用户是否再次改变(就像我们按下光标 3 次时所做的那样)。如果看起来他不打算这么做，执行价值改变。等待的时间是微妙的:太短没有效果，而等待太长就像原因一样糟糕。实际上，大约 250-350 毫秒的时间是好的。对于 DataDigger，我从 350 毫秒开始，但后来又回到 300 毫秒，因为它感觉有点快。

运行 wCustPerSalesrep2.w 并使用光标键浏览销售代表记录。如果你足够快，你会看到，只要你不断换表，客户浏览就不会改变。释放按键，过一会儿客户浏览就会刷新。

让我们看看代码。浏览的值改变了一点:

```
ON VALUE-CHANGED OF brSalesrep IN FRAME DEFAULT-FRAME
DO:
  chCtrlFrame:PSTimer:INTERVAL = 300.
  chCtrlFrame:PSTimer:ENABLED = TRUE.
END. 
```

Enter fullscreen mode Exit fullscreen mode

现在可以在 pstimer.tick 过程中找到实际刷新客户浏览的代码:

```
PROCEDURE CtrlFrame.PSTimer.Tick.
  {&OPEN-QUERY-brCustomer}
  chCtrlFrame:PSTimer:ENABLED = FALSE.
END PROCEDURE. 
```

Enter fullscreen mode Exit fullscreen mode

你可能会注意到，我会时不时地关掉计时器。这是因为我们希望每次有新记录时，计时器都重新开始计时。在 timer.tick 中，我们关闭计时器。毕竟:我们只需要等到用户选择另一条记录。

#### 扭捏导致扭捏

你会注意到，一旦你开始调整，你经常会无休止地调整。运行程序，用鼠标指向一个销售代表。为什么程序现在还在等待？作为用户，你准确地指出了你心目中的销售代表。不用等节目了。因此，您可能希望取消鼠标选择单击事件中代码的注释，使其看起来像:

```
ON MOUSE-SELECT-CLICK OF brSalesrep IN FRAME DEFAULT-FRAME
DO:
  {&OPEN-QUERY-brCustomer}
  RETURN NO-APPLY.
END. 
```

Enter fullscreen mode Exit fullscreen mode

这将解决用鼠标选择时的小延迟。再次运行它，您会注意到，一旦您用鼠标选择，客户浏览会立即刷新

就是这样。如果你在自己的项目中使用了这样的技术，请告诉我。在下一篇文章中，我会告诉你如果你有多个需要处理的延迟事件该怎么做。现在:玩得开心！