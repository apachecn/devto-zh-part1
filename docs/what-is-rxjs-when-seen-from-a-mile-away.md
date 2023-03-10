# 从一英里外看 RxJS 是什么？

> 原文：<https://dev.to/sammyisa/what-is-rxjs-when-seen-from-a-mile-away>

假设我在一个网站上移动鼠标，路径中的每个位置都被收集并存储在一个数组中。在访问结束时，你会得到一个巨大的数组，里面有我访问你的网站时鼠标走过的整个路径。

现在，假设当鼠标在屏幕上移动时，我可以实时访问这些数据。你可以把这个信息看作是一个*数据流*，在任何给定的时间，它都会告诉你我的鼠标在哪里。

既然已经访问了一个*数据流*，我们只需要做一些事情让*对这个数据流做出反应*(也就是说，每当记录到一个新的鼠标移动时就做一些事情)。

为了简单起见，我们将把鼠标在网站上的 X 和 Y 坐标记录到屏幕上。

在 RxJS 中，它看起来像这样:

```
//The *stream*
const mouseMovement$ = Rx.Observable.fromEvent(document, 'mousemove');
//The *do something with it*
mouseMovement$.subscribe(position => console.log(`(${position.cilentX}, ${position.cilentY}`)); 
```

太好了！但是...难道我们不能用事件处理程序来实现吗？

良好的...

```
document.body.addEventListener('mousemove', (event) => {
    console.log(`(${event.clientX}, ${event.clientY})`);
}) 
```

在这个简单的例子中，是的——这也很简单...但是，如果我们想在处理信息之前修改它，该怎么办呢:

```
//I want the distance from the center of the page
const mouseMovement$ = Rx.Observable
    .fromEvent(document, 'mousemove')
    .map( event => {
        const posX = event.clientX - document.body.clientWidth/2;
        const posY = event.clientY - document.body.clientHeight/2;
        return Math.sqrt(posX*posX + posY*posY);
    });
//The *do something with it*
mouseMovement$.subscribe(radius => console.log(`Radius: ${radius}`)); 
```

或者想要限制我们从信息流中获取信息的频率:

```
//I want to know where the mouse pointer is 2.5 seconds and not more often than that
const mouseMovement$ = Rx.Observable
    .fromEvent(document, 'mousemove')
    .debounceTime(2500);
//The *do something with it*
mouseMovement$.subscribe(position => console.log(`(${position.cilentX}, ${position.cilentY}`)); 
```

RxJS 可以很容易地对我们在不同时间点获得的信息进行操作*。当我们把一切都看作是我们想要随着时间做些什么的数据流时，RxJS 开始变得越来越有意义。*

在我的下一篇文章中，我们将使用 RxJS 制作一个 typeahead！因为我是在学习的过程中写的这篇文章，所以这篇文章会有一些我在这个过程中犯的错误。 *[哦嘿这里是帖子！](https://dev.to/sammyisa/rxjs-in-practice-how-to-make-a-typeahead-with-streams)T3】*

* * *

*感谢[工具日](https://twitter.com/toolsday)播客的启发和[大卫·皮亚诺](https://twitter.com/DavidKPiano)的帮助！*