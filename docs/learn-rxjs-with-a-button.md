# “一键学习 RxJS”

> 原文：<https://dev.to/vincecampanale/learn-rxjs-with-a-button>

欢迎光临。这篇文章的目的是教你 Javascript 的反应式扩展(RxJS)的基础。我只是简单介绍一下这个非常酷的库，让你知道可以用它做什么，但是还有很多东西需要学习。

我自己仍然在生产中使用 RxJS，所以如果你正在阅读这篇文章并有经验和反馈，请在 Twitter 上联系我(页脚中的句柄)或给我发电子邮件——不要退缩！如果你对 RxJS 完全陌生，不要担心，我在这篇文章中没有对之前的知识做任何假设。

我将基于 Lukas Ruebellke 在本次演讲中介绍的第一个例子。

克隆[这个回购](https://github.com/vincecampanale/learn-rxjs-with-a-button)在本地获得种子。你也可以通过`completed`分支`checkout`来查看最终结果(以及本指南ðÿ•未涉及的额外功能)。

你不需要知道 Angular 来跟随，只需按照自述文件中的说明，打开`src/app/app.component.ts`就可以了。在`AppComponent`类的`ngOnInit()`方法中会有一个注释——当我一行一行地讲述时，用代码替换那个注释。我鼓励你去尝试，看看随着我们的进步，你还能创造出什么样的酷流。

## 按钮

我们将与之交互的代码部分位于根组件的`template`属性中。

我也在这里提供了它，这样你可以看到它，以防你不想克隆这个项目并安装/服务它:

```
<button #btn md-raised-button color="accent">
    Button
</button>

<div class="container">
    <h1>{ { messages } }</h1>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个按钮和一条消息。

我们将监听这个按钮上的点击事件，并在按钮被点击时更新消息。

### 创建点击流ðÿÿ

就像一股水流向山下，时间是朝一个方向流动的，连续不断，没有间断。现在，想象一块石头掉进一条流动的小溪。会溅起水花。RxJS 允许您对 UI 事件做出响应，就像流对落下的石头做出响应一样。

作为一个例子，让我们将特定按钮上的点击事件建模为一个流。

这里有一个方便的图表:

```
-----------x-----x-----x---> 
```

Enter fullscreen mode Exit fullscreen mode

这里的箭头代表时间，你可以把每个`-`看作一个离散的时刻。让我们假设这个流表示屏幕上的一个按钮。久而久之，用户可以点击也可以不点击上述按钮。每个`x`表示用户点击了按钮，从而触发了一个‘click’事件。

```
const rxBtn = this.getNativeElement(this.btn);       // get the button element
const click$ = Observable.fromEvent(rxBtn, 'click'); // listen for clicks 
```

Enter fullscreen mode Exit fullscreen mode

那还不算太糟。我们正在创建一个点击流，这是一个`Observable`(现在不要太担心这个，但是请花一点时间想想一个`Observable`仅仅基于它的名字是什么)。

**注意:**处理可观察流的一个常见约定是用`$`结束流变量。它基本上是“流”的缩写——例如`clickStream`变成了`click$`。

### RxJS 运算符

运算符是我们在处理可观察对象时可以使用的方法。RxJS 操作符鼓励*声明式编程*，意味着不是告诉计算机*如何*做你想要的事情(即`for`循环)，你只是告诉它*你想要什么*(即`map( from this => to that )`)。

##### 【开始相切】

使用*声明式*编程对数组中的数字进行双精度处理的简单示例:

```
// not declarative :( 
const a = [1, 2, 3];
const double = arr => {
    for ( let i = 0; i < arr.length; i++ ) {
        arr[i] = arr[i] * 2;
    }
    return arr; 
}
double(a); // [2, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

```
// declarative :) 
const a = [1, 2, 3];
const double = arr => arr.map( x => x * 2 );
double(a); // [2, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

旁注:这两个块之间还有另一个区别——后者返回一个新数组，前者只是改变了原始数组。总是更喜欢没有突变的方法*。*

##### 【结束切线】

好吧，回到手头的任务。

如果你到了`app.component.ts`文件的顶部，你会看到几个`import`语句，如下所示:

```
import 'rxjs/add/observable/fromEvent';
import 'rxjs/add/observable/timer';

import 'rxjs/add/operator/filter';
import 'rxjs/add/operator/map';
import 'rxjs/add/operator/debounceTime';
import 'rxjs/add/operator/buffer'; 
```

Enter fullscreen mode Exit fullscreen mode

这些是我们将在这个例子中使用的所有操作符。

让我们从点击流开始，把它分成 250 毫秒的片段。这给了我们的用户足够的时间来双击，但不会太多，所以他们不会不耐烦。为了做到这一点，我们将组合两个有用的操作符:`debounceTime()`和`buffer()`。

#### **【去抖时间】**

分割点击流的第一步(`click$`)是根据输入之间的时间去抖。换句话说，当用户点击时，我们启动一个 250 毫秒的计时器。如果用户在计时器运行时再次单击，计时器将再次开始计时。去抖流不会发出，直到计时器运行完成(250 毫秒过去，用户没有点击)。

在代码中，它看起来像这样:

```
const debounced$ = click$.debounceTime(250); 
```

Enter fullscreen mode Exit fullscreen mode

如果你`console.log``debouncedClicks$`这样:

```
debounced$.subscribe(console.log); 
```

Enter fullscreen mode Exit fullscreen mode

...你应该看看...

```
MouseEvent {isTrusted: true, screenX: 3046, screenY: 239, clientX: 161, clientY: 132…} 
```

Enter fullscreen mode Exit fullscreen mode

...在控制台里。

正如你所看到的，我们给了用户双击的时间，但是只发出了一个事件！那么，我们如何收集去抖的点击呢？

#### **【缓冲()】**

缓冲是这样工作的:

假设这是我们的`click$`事件流(箭头表示时间，`x`表示点击)。

```
----------x---x-------x----x---x-x----x-> 
```

Enter fullscreen mode Exit fullscreen mode

缓冲器将收集输出值，直到*提供的可观察*发出为止所以我们需要给`buffer()`一个*可观测值*作为我们的第一个自变量。然后，Buffer 将输出值收集到一个桶中，直到所提供的可观察值“发出”，这时它将把那个桶放在一边，并开始收集新的桶。碰巧我们有一个`debounceTime()`事件在 250 毫秒的静默点击后事件之后发出。让我们将这 250 毫秒窗口中发生的所有点击事件收集到一个桶中。

```
 *   = `debounced$` observable emits

   ==  = 250 milliseconds

--x--> = `click$` observable

|____| = `buffer` bucket

        ==*      ==*       ==* ==*
----------x--x--------x------x-x----x----->
     |____|      |_|    |____| |_| 
```

Enter fullscreen mode Exit fullscreen mode

请注意，桶在`debouncedClicks$`发射时结束。

现在，代码应该很容易理解。如果不是，就发微博给我(不是开玩笑，省得我尴尬)。

```
const buffered$ = clicks$.buffer(debounced$); 
```

Enter fullscreen mode Exit fullscreen mode

回顾我们到目前为止的代码:

```
const rxBtn = this.getNativeElement(this.btn);       // get the button element
const click$ = Observable.fromEvent(rxBtn, 'click'); // listen for clicks

const debounced$ = click$.debounceTime(250); // debounce the click stream
const buffered$ = click$.buffer(debounced$); // buffer the debounced stream 
```

Enter fullscreen mode Exit fullscreen mode

下一步是找到一种方法来计算每个 bucket 中的点击次数，这样我们就可以通过两次点击来精确定位 bucket。

#### **地图()**

不要和 [`Array.prototype.map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map?v=example) 混淆，这是 [`Observable.map()`](http://reactivex.io/documentation/operators/map.html) 。它和`Array.prototype.map()`做同样的事情，但是有~可观察到的~。

在这一步中，我们将做一些简单而强大的事情。

每个缓冲桶都是一个`MouseEvents`(在本例中为点击)的数组。如果我快速连续点击按钮三次，看起来是这样的:

```
buffered$.subscribe(console.log); // [MouseEvent, MouseEvent, MouseEvent] 
```

Enter fullscreen mode Exit fullscreen mode

就像任何 Javascript 数组一样，这个数组有一个`.length`属性，我们将用它来计算这个桶中的点击次数。

让我们创建一个接受数组并返回其长度的函数:

```
const toLength = a => a.length; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将此应用于缓冲的点击流，以获得每个桶中的点击次数:

```
const clickCount$ = buffered$.map(toLength); 
```

Enter fullscreen mode Exit fullscreen mode

太好了。我们已经将我们的点击量转化为计数。但是，我们仍然没有孤立的双击。

#### **滤镜()**

假设我们有一个数字数组`a = [1, 2, 3, 2, 2, 1]`，我们只想保留`2`并将它们移动到一个新的数组中。我们的`filter()`调用看起来像`a.filter(x => x === 2)`。

嗯，可观察的事物也有一个`filter()`！

```
const doubleClick$ = clickCount$.filter(x => x === 2); 
```

Enter fullscreen mode Exit fullscreen mode

产生的可观察对象(`doubleClick$`)现在只会在用户双击按钮时发出！

现在我们可以响应这个事件并更新消息了！

### **订阅()**

我已经在这篇文章的前面展示了`.subscribe()`的作用，在`debounceTime()`和`buffer()`部分，我用它来记录控制台的`debounced$`和`buffer$`可观察流的内容。与杂志类似，除非你*订阅*，否则你不会从一个可观看的信息流中接收到任何内容。

我们想要订阅我们的`doubleClick$`可观察对象，并通过更新消息为`"Double click!"`来响应它的事件。

```
doubleClick$.subscribe(event => this.message = 'Double click!'); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！真的就那么简单。不，这不是陷阱。

请注意，我们将双击事件映射到完全不相关的东西。事件本身对我们没用，我们需要的只是知道它发生了。当事件发生时，我们如何处理完全取决于我们自己。虽然我们在这里所做的从技术上来说是一个副作用，而且有一大堆的蠕虫，但我将忽略这一点，并专注于这样一个事实，即一旦我们掌握了这个可观察到的流，我们就可以对它做任何我们想做的事情。

总结一下，下面是我们在本指南中构建的整个代码块:

```
const toLength = a => a.length; // helper -- gets length of given array

const rxBtn = this.getNativeElement(this.btn);       // get the button element
const click$ = Observable.fromEvent(rxBtn, 'click'); // listen for clicks

const debounced$ = click$.debounceTime(250); // debounce the click stream
const buffered$ = click$.buffer(debounced$); // buffer the debounced stream

const clickCount$ = buffered$.map(tolength);            // get buffer lengths
const doubleClick$ = clickCount$.filter(x => x === 2);  // filter for length 2

doubleClick$.subscribe(event => this.message = 'Double click!'); 
```

Enter fullscreen mode Exit fullscreen mode

注意:可观察的方法可以像任何其他 Javascript 方法一样被链接和组合。有时候，为了可重用性和整洁性，对流进行划分是很好的，但是有时候，消除中间变量也是很好的。

检查一下:

```
const rxBtn = this.getNativeElement(this.btn);       // get the button element
const click$ = Observable.fromEvent(rxBtn, 'click'); // listen for clicks

click$
    .buffer(click$.debounceTime(250))
    .map(a => a.length)
    .filter(x => x === 2)
    .subscribe(e => this.message = 'Double click!'); 
```

Enter fullscreen mode Exit fullscreen mode

#### 奖励挑战:

1)制作一个清除按钮来清除消息(使用 observables！).

2)当用户按住 Shift 键点击按钮时，更新消息为 *~Shift Click~* 。

3)构建一个函数，该函数采用一个数字和一个点击流，并返回一个包含该数字的点击的新流(即`filterClickCount$(click$)(3)`)返回一个三次点击流。注意，我用`$`结束了函数名，因为它返回一个流。 *[阿谀奉承](http://www.vincecampanale.com/blog/2017/04/22/what-is-currying/)随意但鼓励！*

**这些挑战的解决方案在 git 回购的`bonus`分支中。**

#### 额外资源

*   这篇文章的灵感来自于我在 Lukas Reubellke 的课程 [Hello RxJS](https://courses.ultimateangular.com/p/hello-rxjs) 中的漫步。
*   Lukas 还在 RxJS 上做了一个[演讲](https://www.youtube.com/watch?v=5CTL7aqSvJU)，在这篇文章的顶部提到过。
*   Andre Staltz 写了一篇关于反应式编程的精彩、深入的文章:[你一直错过的反应式编程介绍](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)。