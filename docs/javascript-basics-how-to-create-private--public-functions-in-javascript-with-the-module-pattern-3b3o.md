# JavaScript 基础:如何用模块模式在 JavaScript 中创建私有和公共函数

> 原文：<https://dev.to/mrfrontend/javascript-basics-how-to-create-private--public-functions-in-javascript-with-the-module-pattern-3b3o>

[![](img/cf750a51b99bd8678d2286fe9c5d11c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--LMyeBiKA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AtYTDNJnDLKxf4tbmAgJFPg.png)

我将向您展示如何使用模块模式(对象文字)用普通 JavaScript 创建私有函数和公共方法。

#### JavaScript 模块模式

在我看来，JavaScript 模块模式是将 JavaScript 构建成更具可重用性的代码片段的方法。

但是今天我不打算谈论模块模式的所有内容。今天我将向你展示如何创建私有函数和公共方法。

[https://www.youtube.com/embed/bEYjWhqrt_0](https://www.youtube.com/embed/bEYjWhqrt_0)

#### 私有函数

私有函数是私有的，因为它不能从模块外部访问。

#### 公共方法

公共方法就像函数中的私有方法一样。但是对象内部的函数叫做方法。

也许有经验的开发者会说，我的解释太简单了。请在评论中说明！

#### 自调用-函数-表达式

JavaScript 模块是由变量创建的。在里面我们放了一个“自调用函数表达式”。这个函数是自动调用的。

说够了，直接进入代码吧！

#### JavaScript 模块

让我们创建一个名为 MrFrontendModule 的模块。

```
var MrFrontendModule = (function() {
  let mrFrontendMethods = {};
  let title = 'The Mr Frontend JavaScript Module';

  let addEmoticon = function() {
    return title + ' ';
  }

  mrFrontendMethods.getTitle = function() {
    return addEmoticon();
  }

  return mrFrontendMethods;
})(); 
```

在模块中，我们有:

*   作为对象的 let mrFrontendMethods 变量
*   包含标题的字母标题变量
*   一个 let addEmoticon 函数，它返回标题变量和一个附加的表情符号。
*   一个 getTitle 方法，它连接到 mrFrontendMethods 对象
*   返回 mrFrontendMethods 对象的 return 语句

这个模块里面的 **private** 是什么？

*   标题变量
*   addemotion 函数

这些东西是私有的，因为它们没有被 return 语句返回。因此在模块外部无法访问它们。

这个模块里面的 **public** 是什么？

*   mrFrontendMethods 对象
*   getTitle 方法

这些东西是公共的，因为它们是由 return 语句返回的。可以通过模块访问，在模块外使用。

#### 如何使用公有和私有的方法和函数？

我们创建了这个模块，因为我们希望它可以在我们的其他模块中重用。

让我们从使用 MrFrontendModule 开始。这很简单，只需像这样调用方法:

```
console.log(‘Title: ‘, MrFrontendModule.getTitle()); 
```

当这段代码被执行时，你会看到这样的结果:“*的*先生*前端 JavaScript 模块*”。

这是意料之中的！

如果我们试着这样调用函数 addEmoticon 呢？

```
console.log(‘Title: ‘, MrFrontendModule.addEmoticon()); 
```

我们会得到这个错误:

```
"TypeError: MrFrontendModule.addEmoticon is not a function 
```

这是正确的，因为这个函数不是由模块返回的，所以基本上对模块外的所有东西都是隐藏的。

所以我们只能在 MrFrontendModule 内部调用 functionaddEmoticon。

#### 如何使用另一个 JavaScript 模块内部的公共方法？

```
var SecondModule = (function(MrFrontendModule) {
  let secondModuleMethods = {};

  secondModuleMethods.getTitleFromOtherModule = function() {
    return MrFrontendModule.getTitle();
  }

  return secondModuleMethods;
})(MrFrontendModule); 
```

#### 你会如何处理私人和公共事务？

如果你有另一种方法来使用私有函数和公共函数？

您更喜欢使用哪种 JavaScript 模块模式？

请在评论里告诉我！

如果你想搜索更多关于这个模块模式的信息，我强烈推荐[去 Todd 座右铭的网站](https://toddmotto.com/mastering-the-module-pattern/)上看看。

#### **你想由我来训练吗？**

和 Frontend 先生一起，我想帮助你更专注于重要的事情和有助于你成长的事情。

如果你想让我帮你更多，支持我的博客、视频和播客，然后去[https://patreon.com/mrfrontend](https://patreon.com/mrfrontend)选择你想花的钱数。(几乎)每笔金额都有一个感谢包！

有几个套餐里面有一些个人训练的瞬间，快去看看吧！

*原载于* [*奥前端博客*](https://blog.mrfrontend.org/2017/11/javascript-basics-create-private-public-functions-javascript-module-pattern/) *。*

* * *