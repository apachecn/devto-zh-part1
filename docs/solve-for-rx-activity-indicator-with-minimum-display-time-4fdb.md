# 求解 Rx:具有最小显示时间的活动指示器

> 原文：<https://dev.to/jonstodle/solve-for-rx-activity-indicator-with-minimum-display-time-4fdb>

# 求解 Rx

我将在我的博客上开始一个循环系列，名为*解决 Rx* 的问题，在这个系列中，我使用 Rx 解决不同的问题。我认为这不仅有助于提高我的技能，而且我还相信，除了预输入搜索框示例之外，其他人也会发现看到一些实用的示例是有帮助的。

如果你对 Rx 要解决的问题有任何建议，我很想听听！请在下方留言或在 [Twitter](http://twitter.com/jonstodle) 上联系我。

# 显示时间最短的活动指示器

想象你为你的应用程序做了一个奇特的活动指示器。每次用户必须等待某件事情发生时，你都要显示它。也许在网络请求期间。我做了一个简单的例子:

[![Pinwheel](img/16d2471b3d9bc6eefb68aba88f6eaaa2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a3hBXSjt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.jonstodle.com/conteimg/2017/10/pinwheel.gif)

## 第一次实现

在第一个实现中，当您单击 load 按钮时，它模拟一个 web 请求。现在，模拟 web 请求的响应时间是半秒。活动指示器的动画需要 2 秒钟才能完成。

虽然这意味着我不能展示我的精彩动画，但这也意味着用户可能没有意识到这个动作已经完成——甚至一开始就没有开始。有时候，当你看到一项工作需要一点时间时，你会觉得这项工作完成得很好。

```
const Observable = Rx.Observable;
const button = document.querySelector('#button');
const pinwheel = document.querySelector('#pinwheel');
function makeWebRequest() { // 1
    return Observable.timer(500);
}

const buttonClick$ = Observable.fromEvent(button, 'click').share(); // 2
const webRequest$ = buttonClick$.switchMap(_ => makeWebRequest()).share(); // 3

// Simulate handling web requests
webRequest$.subscribe(); // 4

buttonClick$
    .map(_ => true) // 5
    .merge(webRequest$.map(_ => false)) // 6
    .subscribe(spin => { // 7
        if (spin) {
            pinwheel.classList.add('spin');
        } else {
            pinwheel.classList.remove('spin');
        }
    }); 
```

参见 Codepen 上的示例

参见 [CodePen](https://codepen.io) 上 Jon stdle([@ jonstodle](https://codepen.io/jonstodle))的 Pen [blog-50](https://codepen.io/jonstodle/pen/eGPPwZ/) 。

1.  这个函数通过在 500 毫秒后发出一个信号来模拟一个 web 请求(这是一个快速网络)。
2.  这将在每次点击按钮时发出一个信号。可观测值在所有订户之间共享。
3.  这将在每次 web 请求返回时发出一个信号。确保它只订阅最近的请求。`share`确保请求不是每个订阅者完成一次，而是在所有订阅者之间共享。
4.  这通常是您处理 web 请求的地方:存储数据和/或更新 UI。这里是空的，因为它不是真正相关的。
5.  我们将每个按钮点击映射到`true`。`true`值表示应该显示活动指示器。
6.  我们将按钮点击产生的`true`与 web 请求返回时产生的`false`合并。
7.  如果我们接收到`true`使风车旋转，我们添加一个名为`spin`的类，如果我们接收到`false`，我们删除这个类

短响应时间的结果是:

[![Pinwheel short](img/1da7cef5b7b533257c73672eca69c906.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Up6CUsc8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.jonstodle.com/conteimg/2017/10/pinwheel-short.gif)

## 修复实现

为了解决这个问题，我们希望动画在停止前至少显示 2 秒钟。下面的代码是一样的，直到我们看到编号的注释:

```
const Observable = Rx.Observable;
const button = document.querySelector('#button');
const pinwheel = document.querySelector('#pinwheel');
function makeWebRequest() {
    return Observable.timer(500);
}

const buttonClick$ = Observable.fromEvent(button, 'click').share();
const webRequest$ = buttonClick$.switchMap(_ => makeWebRequest()).share();

// Simulate handling web requests
webRequest$.subscribe();

buttonClick$
    .switchMap(_ => Observable.combineLatest( // 1
        webRequest$.take(1), Observable.timer(2000), // 2
        (_, __) => false) // 3
        .startWith(true)) // 4
    .subscribe(spin => {
        if (spin) {
            pinwheel.classList.add('spin');
        } else {
            pinwheel.classList.remove('spin');
        }
    }); 
```

```
View on Codepen 
```

参见 [CodePen](https://codepen.io) 上 Jon stdle([@ jonstodle](https://codepen.io/jonstodle))的 Pen [blog-50-2](https://codepen.io/jonstodle/pen/YrRjqa/) 。

1.  每次点击按钮，我们都会创建一个新的观察对象，我们只想订阅最近返回的观察对象。
2.  我们将 web 请求的结果与一个计时器结合起来，该计时器将在 2 秒钟(动画的长度)后发出信号。我们还使用`take(1)`来确保我们只在严格需要的时候订阅`webRequest$`。
3.  只有当每个可观察对象(`webRequest$`和`Observable.timer(2000)`)发出至少一个信号时，`combineLatest`才会发出信号。当它们都返回一个信号时，我们将忽略这些值，返回`false`;这是为了停止旋转(如前所述)
4.  我们让可观察对象在被订阅时立即发出一个`true`信号。

新结果是这样的:

[![Pinwheel](img/16d2471b3d9bc6eefb68aba88f6eaaa2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a3hBXSjt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.jonstodle.com/conteimg/2017/10/pinwheel.gif)

* * *

如上所述，这种技术既可以等待动画结束，也可以确保加载的动画显示最短的时间。后者对用户非常有用，因为他们有时间注意到负载已经发生。

编码快乐！