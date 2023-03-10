# 求解 RxJS:按顺序显示消息

> 原文：<https://dev.to/jonstodle/solve-for-rxjs-showing-messages-in-sequence-1c21>

# 求解 Rx

*Solve for Rx* 是我用 Rx 解决不同问题的系列。我认为这不仅有助于提高我的技能，而且我还相信，除了预输入搜索框示例之外，其他人也会发现看到一些实用的示例是有帮助的。

如果你对 Rx 要解决的问题有任何建议，我很想听听！请在下方留言或在 [Twitter](http://twitter.com/jonstodle) 上联系我。

# 按顺序显示消息

用户界面的一个常见特征是向用户显示消息。可能是某些后台任务已经完成，您想要通知用户。在某些情况下，你可能会连续收到多条信息。在这种情况下你会怎么做？

您可以建立某种队列，在那里推送新消息。如果没有显示消息，您会立即显示消息。如果有消息显示，你什么也不做。显示完一条消息后，您可以检查队列中的新消息，并显示下一条消息。这可能会很快变得非常复杂。

我想要的是这样的:

[![Messages being shown sequentially](img/5ef3842658ec2429a6c4287a4f556d4b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xw9CvhXY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jonstodle.com/sol/)

为了演示，我将这个简单的 HTML 放在一起，并对其进行了一些设计:

```
<div id="container">
  <input type="text" id="message-input">

  <h1 id="message-container"></h1>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

在 JavaScript 中，我引用了我感兴趣的两个元素:

```
const messageInput = document.querySelector("#message-input");
const messageContainer = document.querySelector("#message-container"); 
```

Enter fullscreen mode Exit fullscreen mode

我很想知道用户在输入`input`时什么时候按下`Enter`键。这将提交当前消息。我为`keydown`事件创建一个可观察对象，并在按下`Enter`键时让信号通过:

```
Rx.Observable.fromEvent(messageInput, "keydown")
  .filter(event => event.keyCode === 13) 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我创建一个表示显示消息的生命周期的可观察对象。它在`#message-container`中设置新的文本，显示`#message-container`，等待一会儿，然后再次隐藏`#message-container`。

首先，我将事件映射成一个可观察对象，它发送一个包含消息
的*下一个信号*

```
.map(event =>
  Rx.Observable.of(event.target.value) 
```

Enter fullscreen mode Exit fullscreen mode

设置`#message-container`的文本:

```
.do(message => messageContainer.innerText = message) 
```

Enter fullscreen mode Exit fullscreen mode

为了显示和隐藏`#message-container`，我使用了一个 CSS 类来覆盖从`0`到`1`的`#message-container`不透明度。我还添加了一个持续半秒钟的过渡。

```
.do(_ => messageContainer.classList.add("visible")) 
```

Enter fullscreen mode Exit fullscreen mode

通过在三秒钟内产生一个信号的可观察对象，我有效地暂停了可观察链的“执行”三秒钟。这让用户有时间阅读我们的消息:

```
.switchMap(_ => Rx.Observable.timer(3000)) 
```

Enter fullscreen mode Exit fullscreen mode

移除覆盖`#message-container` :
不透明度的 CSS 类

```
.do(_ => messageContainer.classList.remove("visible")) 
```

Enter fullscreen mode Exit fullscreen mode

我又一次看到了一个将在未来产生信号的可观测现象。虽然转换只需要 500 毫秒，但我等待了 750 毫秒，以便在显示消息之间有一点空间:

```
.switchMap(_ => Rx.Observable.timer(750))) 
```

Enter fullscreen mode Exit fullscreen mode

将事件映射到描述消息显示生命周期的可观察值后，我不再需要`#message-input` :
的值

```
.do(_ => messageInput.value = "") 
```

Enter fullscreen mode Exit fullscreen mode

对包含可观测量的可观测量起作用。它将依次订阅每一个内在的可观察事物。这意味着它不会订阅一个新的可观测值，直到前一个已经完成:

```
.concatAll() 
```

Enter fullscreen mode Exit fullscreen mode

由于我们的每个内部观察对象描述了显示单个消息的整个生命周期，并且`concatAll`不会订阅新的观察对象，直到前一个结束，消息将依次显示。

最后，我订阅了使整个事情工作:

```
.subscribe(); 
```

Enter fullscreen mode Exit fullscreen mode

您可以在这个代码栏中看到它的运行:

在 [CodePen](https://codepen.io) 上看到 Jon stdle([@ jonstodle](https://codepen.io/jonstodle))在序列 01 中显示消息的笔[。](https://codepen.io/jonstodle/pen/KZaZqJ/)

## 更进一步

如果可以通过点击来消除消息，那不是很好吗？大概是这样的:

[![Message displays in sequence, but can also be dismissed](img/5ef3842658ec2429a6c4287a4f556d4b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xw9CvhXY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jonstodle.com/sol/)

在等待三秒钟的`switchMap`之后，我添加了一个`takeUntil`，当我点击`#message-container` :
时，它将完成可观察到的结束

```
.switchMap(message => Rx.Observable.timer(3000).map(_ => message))
.takeUntil(Rx.Observable.fromEvent(messageContainer, "click")) 
```

Enter fullscreen mode Exit fullscreen mode

我将移除 CSS 类并等待消息的部分隐藏在一个`concat` :
中

```
.concat(Rx.Observable.of(null)
    .do(_ => messageContainer.classList.remove("visible"))
    .switchMap(_ => Rx.Observable.timer(750)))) 
```

Enter fullscreen mode Exit fullscreen mode

`concat`将给定的可观察对象附加到被调用的可观察对象`concat`上。当第一个可观察的结束时，附加的开始。对订户来说，它看起来像是一个可观测的。

这个`concat`确保无论三秒计时器正常结束，还是通过`takeUntil`触发提前结束，都完成了`#message-container`的隐藏。

您可以在这个代码栏中看到它的运行:

请看乔恩·斯特德尔( [@jonstodle](https://codepen.io/jonstodle) )在 [CodePen](https://codepen.io) 上按顺序完成的显示信息的笔[。](https://codepen.io/jonstodle/pen/wpgpQQ/)

* * *

编码快乐！