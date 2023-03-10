# 用 Rxjs 调节 Angular 中的事件

> 原文：<https://dev.to/martinmcwhorter/throttling-events-in-angular-with-rxjs-105c>

Angular 和 Rxjs 都能让你轻松听事件。使用 Angualr 的@HostListener()注释允许声明性绑定，而使用 Rxjs 事件绑定允许事件的过滤、去抖动和节流等等。

绑定到角度组件中的事件的首选方式是使用`@HostListener()`注释。这提供了一个从事件到方法的清晰的声明绑定，最好的是不需要访问任何浏览器特定的 API，比如`document`或`window`。如果您想使用通用服务器端呈现或在 WebWorker 中运行应用程序，避免直接浏览器 DOM APIs 就变得很重要。让您的代码对这些可能性保持开放，即使您目前没有计划，也可以让您利用未来的优化。

### 节流调整大小

你可能会问，*为什么我需要监听调整大小事件？*

你可能不需要。尽可能多的 UI 元素应该通过 CSS 提示而不是脚本提示来响应外形的变化。移动设备和平板设备没有任何真实的故事，其中视窗的大小变化不由`deviceorientation`事件处理。它们可能是一些边缘情况，如分屏和一些虚拟键盘推上浏览器窗口。

这可能是一些例外，您需要编写响应脚本。例如，您可能有一个菜单，它在桌面外形上是固定打开的，在平板电脑上以最小化模式固定在横向模式下，而在纵向平板电脑和手机上隐藏在汉堡菜单后面。

即使在这种情况下(用户正在调整大小而不是旋转的有效情况)，浏览器窗口充其量也只是一种边缘情况。

实际上，用例是开发人员的故事。作为一名开发人员，您希望确保您的 UI 在最小可接受宽度到最大宽度之间的每个宽度上都能正常工作。您希望能够将响应灵敏的浏览器开发工具从微小扩展到超大，并确保应用程序能够正确显示每个像素宽度。

<figure>

有了 Rxjs，我们可以非常简单地完成:

<figcaption></figcaption>

</figure>

```
private resizeObservable = Observable.fromEvent(
    window, 
    'resize'
  ).throttleTime(200); 
```

Enter fullscreen mode Exit fullscreen mode

漂亮又干净。但这有一个问题。我们正在直接访问 DOM `window`。我们可以将它包装在一个 try/catch 中，或者将一个*窗口*注入到我们的组件中，如果我们在服务器上运行，这个窗口就会被替换为一个假的，但是如果我们决定在一个 WebWorker 中运行，我们就不走运了，需要替换这个代码。

### 解

我们可以将 Rjxs throttle 方法与 Angular 的`@HostLister()` decorator 注释结合使用。

在下面的例子中，我们创建了一个类型为*编号为*的*主题*，命名为`resizeSubject`。我们将在事件处理程序中调用`resizeSubject`上的`next()`方法。

<figure>

我们从我们的*主体*中创建一个*可观测的*，并节流 200 毫秒。然后在我们的`ngOnInit()`中，我们将*订阅*我们的节流可观察值。

<figcaption>app.component.ts</figcaption>

</figure>

```
export class AppComponent implements OnInit { 

  private resizeSubject = new Subject<number>(); 
  private resizeObservable = this.resizeSubject.asObservable()
    .throttleTime(200); 

  @HostListener('window:resize', ['$event.target.innerWidth']) 
  onResize(width: number) { 
    this.resizeSubject.next(width); 
  } 

  ngOnInit() { 
    this.resizeObservable.subscribe(x => 
      this.doSomethingWithThrottledEvent(x)); 
  } 

  private doSomethingWithThrottledEvent(width: number) { 
    // . . . 
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode