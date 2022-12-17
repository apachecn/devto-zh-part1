# 如何理解面向接口而非实现的编程

> 原文：<https://dev.to/dawncold/how-to-understand-program-to-an-interface-not-an-implementation>

昨天我和同事谈到 JS `for loop`和`const`。我发现了这个[的帖子](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-closure-b2f0d2152b36)，它引用了一句名言“程序是一个接口，而不是一个实现”，我几年前就读过，对此没有任何感觉，只是觉得这是一句很酷的名言或规则。

现在，经过几年的工作经验，我对此有了一些了解。

例如我用了一个简单的 JS 例子，

```
var counter = function() {
    var count = 0;
    return {
        incr: function(){count++;},
        decr: function(){count--;},
        disp: function(){console.log(count)}
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我暴露了三个函数:`incr`、`decr`、`disp`，他们操作私有的`count`变量，在外侧，被调用者不知道`count`，被调用者可以调用`incr`、`decr`、`disp`，三个函数是`counter`的接口。

`counter`另一种实现是公开`count`，被调用者通过直接操作`count`变量来增加计数、减少计数、显示计数。

当`incr`、`decr`逻辑改变时，例如计数增加 2，而不是 1。接口和以前一样，被调用者不需要改变任何代码。