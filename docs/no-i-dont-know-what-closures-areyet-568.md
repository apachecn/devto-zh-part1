# 不，我不知道什么是闭包...然而

> 原文：<https://dev.to/hawicaesar/no-i-dont-know-what-closures-areyet-568>

我想分享我在闭包上学到的有趣的一课。这一课的旅程始于我对一个 PR 的评论，PR 的作者说他将额外的`()`添加到一个函数`this.handleToggle`中，因为该函数是一个闭包。这让我绞尽脑汁了一阵子。我的意思是，我知道什么是闭包，或者至少是一个概念。我决定把它记在我的笔记本上，因为我以后会再来看它。然后我想起我的一个朋友推荐我在调查 js 时从这里的[开始](https://github.com/getify/You-Dont-Know-JS)。我很快去了这个 github repo，寻找关于闭包的章节。我以为我会找到的教科书定义不在那里。然而，凯尔·辛普森用例子解释说“对我来说，就像我 5 岁一样”,我理解了这个概念。

让我困惑的一个有趣的代码片段是这个

```
for (var i=1; i<=5; i++) {
    setTimeout(() => {
        console.log( i );
    }, 100 );
} 
```

Enter fullscreen mode Exit fullscreen mode

你可能认为打印出来的是

```
1
2
3
4
5 
```

Enter fullscreen mode Exit fullscreen mode

这实际上在控制台上打印了 5 次。试试看。奇怪吗？不完全是。我只是想看看上面的输出。我用它玩了一圈，仍然在控制台上得到 6 个 5 次。为什么没有继续读下去，是因为我认为我知道，我一开始读的内容足以在不进一步阅读的情况下尝试循环示例。我甚至加了一个 0 来超时，不能有任何延迟。没有运气！我继续读凯尔·辛普森的书，发现他有一个解决办法。事实证明，我(以及许多开发人员可能已经这样做了)试图

`imply that each iteration of the loop "captures" its own copy of i, at the time of the iteration. But, the way scope works, all 5 of those functions, though they are defined separately in each loop iteration, all are closed over the same shared global scope, which has, in fact, only one i in it.` 

Kyle Simpson 提供了一个解决方案，要么使用 IIFE，要么使用 ES6 的兄弟`var`、`let`。

利用生命

```
for (var i=1; i<=5; i++) {
    (function(j){
        setTimeout(() => {
            console.log( j );
        }, j );
    })( i );
} 
```

Enter fullscreen mode Exit fullscreen mode

使用`let`

```
for (let i=1; i<=5; i++) {
    setTimeout(() => {
        console.log( i );
    }, 100 );
} 
```

Enter fullscreen mode Exit fullscreen mode

我找到一位高级开发人员，他解释了同样的问题，只是强调了`var`有多危险。`var`提升 I 的值，因此总是打印值 6。我忘记问的是这个。对于第一个代码片段，整个循环的运行是否使得 var 达到 6，然后运行所有的`setTimeouts`,然后显示 6 作为 I 的值？

最后，什么是闭包:

那些喜欢抓住定义不放的人，我做了一些调查，Eric Elliot 在这里做了很好的解释。

埃里克·埃利奥特
 `A closure is the combination of a function bundled together (enclosed) with references to its surrounding state (the lexical environment). In other words, a closure gives you access to an outer function’s scope from an inner function.` 

我得到了一个凯尔·辛普森的视频，他解释如下

`A closure is when a function 'remembers' its lexical scope even when that function is executed well outside it the lexical scope.`

我希望它们有意义，我鼓励你多读一些关于
[“你不知道 Js，闭包”](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch5.md)
[掌握 JavaScript 采访:什么是闭包？](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36)