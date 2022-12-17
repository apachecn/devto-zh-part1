# 关于异步/等待和承诺的问题

> 原文：<https://dev.to/maxart2501/gotchas-about-asyncawait-and-promises-9di>

JavaScript 一直具有异步的特性。尽管大多数网络 API 是同步的，但事情最终还是发生了变化，这也要归功于函数在 JavaScript 中是一等公民。现在，基本上每个新的 JavaScript API 都被设计成异步的。(甚至几十年前的 cookies 也可能得到[的异步重修补](https://github.com/WICG/async-cookies-api)。)

当我们不得不*序列化*那些异步任务时，问题就来了，这意味着在回调结束时执行一个异步方法，等等。在实践中，我们不得不这样做:

```
$.get('/api/movies/' + movieCode, function(movieData) {
  $.get('/api/directors/' + movieData.director, function(directorData) {
    $.get('/api/studios/' + directorData.studio, function(studioData) {
      $.get('/api/locations/' + studioData.hq, function(locationData) {
        // do something with locationData
      });
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

没错，那就是末日金字塔。(这只是一个简单的例子:当你不得不并行执行异步任务*时，事情就变得疯狂了。)*

 *然后`Promise` s 来了，还有 ES2015。与...嗯，*答应*把我们的代码变成这样:

```
doSomething()
  .then(data => doStuff(data))
  .then(result => doOtherStuff(result))
  .then(outcome => showOutcome(outcome)); 
```

Enter fullscreen mode Exit fullscreen mode

好看，易读，有语义。在实践中，比预期更多的是，我们最终得到了这样的结果:

```
doSomething().then(data => {
  doStuff(data).then(result => {
    doOtherStuff(data, result).then(outcome => {
      showOutcome(outcome, result, data);
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

又是金字塔！发生了什么事？！

当一项任务不仅依赖于前一项任务的结果，还依赖于前一项任务的结果时，这种情况就会发生。当然，你可以这样做:

```
let _data;
let _result;
doSomething().then(data => {
  _data = data;
  return doStuff(data);
}).then(result => {
  _result = result;
  return doOtherStuff(_data, result);
}).then(outcome => {
  showOutcome(outcome, _result, _data);
}); 
```

Enter fullscreen mode Exit fullscreen mode

我甚至不会开始指出这是多么尴尬和不和谐。我们在赋值之前声明了我们需要的变量，如果你和我一样，患有“必须使用- `const`”的强迫症，每当一个变量的值不被期望改变时，你会觉得那些`let`就像刺在你的瞳孔里。

但是后来 ES2016 来了，它带来了`async` / `await`的甜蜜！承诺(...)把我们的混乱变成类似同步的代码:

```
const data = await doSomething();
const result = await doStuff(data);
const outcome = await doOtherStuff(data, result);
await showOutcome(outcome, result, data); 
```

Enter fullscreen mode Exit fullscreen mode

**好看！**

但是...和往常一样，事情并不总是那么容易。让我们看看。

## 没有什么承诺是不能兑现的

这一点尤其正确，因为拒绝承诺是*而不是*抛出的错误。尽管最近浏览器和 Node 变得更智能了，但是带有未处理拒绝的承诺过去常常失败*无声无息*...而且致命。更不用说调试的一塌糊涂了。

现在，当一个被拒绝的承诺发生了什么？

它会呕吐。

你可能会认为，解决这个问题很容易。我们已经有`try...catch`很久了:

```
try {
  const data = await doSomething();
} catch (e) {
  console.error('Haha, gotcha!', e.message);
} 
```

Enter fullscreen mode Exit fullscreen mode

...现在，我必须问。你们当中有多少 JavaScript 开发者觉得*写`try...catch`es*很舒服？JavaScript 一直是一种宽容的语言，大多数时候我们只需要检查一个值是否是`null`或类似的东西。补充一点，JavaScript 在处理`try...catch`时表现不佳，你会有一个尴尬的反应。

(尽管最近情况有些变化。虽然之前 V8 没有优化`try...catch`内部的代码，但随着 V8 6.0 和 Chrome 60 和 Node 8.3 附带的涡扇，情况已经不再是这样了，我猜其他浏览器厂商很快就会赶上来。所以我们将以本地`Promise` s 常见的[性能问题结束。)](https://kyrylkov.com/2017/04/25/native-promises-async-functions-nodejs-8-performance/)

## 范围内的灾难

好吧，我们必须用 5 行`try...catch`来替换我们漂亮的`await`一行。这已经够糟了，但不幸的是还没完。让我们再次检查代码:

```
try {
  const data = await doSomething();
} catch (e) { ... }

// Doing something with data... 
```

Enter fullscreen mode Exit fullscreen mode

唉，我们又倒霉了:*我们不能用`data`* ，因为它超出了我们的范围！事实上，它的作用域只存在于`try`块中！我们如何解决这个问题？

...解决方案也很糟糕:

```
let data;
try {
  data = await doSomething();
} catch (e) { ... }

// Doing something with data... 
```

Enter fullscreen mode Exit fullscreen mode

再次用`let`预声明一个变量...一个差点被逼再次使用`var`！*实际上也没那么糟糕*，因为有了`async` / `await`，你的函数可能会有一个*平坦的*作用域，你的变量无论如何都会有一个闭包作用域。但是棉绒会告诉你代码很烂，你的强迫症会让你睡不着觉，咖啡会变酸，小猫会变得悲伤等等。

我们取得的唯一进步是，我们可以在和`try...catch`块之前使用`let` *，这样事情就不那么不和谐了:* 

```
let data;
try {
  data = await doSomething();
} catch (e) { ... }

let result;
try {
  result = await doStuff(data);
} catch (e) { ... } 
```

Enter fullscreen mode Exit fullscreen mode

## *神奇宝贝*解决方案

如果你在乎小猫快乐，你需要做点什么。这里有一个常见的，简单的，做事情的方法:

```
try {
  const data = await doSomething();
  const result = await doStuff(data);
  const outcome = await doOtherStuff(data, result);
  await showOutcome(outcome, result, data);
} catch(e) {
  console.error('Something went wrong, deal with it 🕶¸', e.message);
} 
```

Enter fullscreen mode Exit fullscreen mode

让我告诉你，你还是睡不着。是的，你“必须抓住他们”，但不是这样。你已经无数次被告知这是不好的，你应该感到不好，*尤其是 JavaScript 中的*，在那里你不能依赖多个`catch`块来区分异常类型，相反你必须用`instanceof`甚至`message`属性来检查它们。

## 照章办事

你用手指发誓你永远不会做那种事，做他们应该做的事。可能的情况:

```
try {
  const data = await doSomething();
  const result = apparentlyInnocentFunction(data);
  return result;
} catch(e) {
  console.error('Error when doingSomething, check your data', e.message);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在捕捉被拒绝的承诺，没错。但是之后会发生什么呢？没什么，我们只是调用一个无辜的(显然)函数来转换数据。

...我们确定吗？这个函数是无辜的吗？

问题是 a `try...catch`是*还是* a `try...catch`。它不仅会捕捉到`await`的承诺，还会捕捉到*所有抛出的错误，不管我们是否期望它们。为了正确地做事，我们应该用`try...catch`来包装*只是*对`await`的承诺。*

丑。啰嗦。痛苦。但是必要的。

我们已经在使用`Promise` s 时看到了这一点，所以这应该不是什么新鲜事。总之不要这样:

```
doSomething.then(data => {
  const result = apparentlyInnocentFunction(data);
  return result;
}).catch(error => {
  console.error('Error when doingSomething, check your data', e.message);
}); 
```

Enter fullscreen mode Exit fullscreen mode

改为这样做:

```
doSomething.then(data => {
  const result = apparentlyInnocentFunction(data);
  return result;
}, error => { // <= catching with the second argument of `then`!
  console.error('Error when doingSomething, check your data', e.message);
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 好好妥协？

那么，我们该如何处理这个烂摊子呢？一个好的解决方案是完全去掉`try...catch`块，利用`Promise` s，记住它们自己有一个`catch`方法，再次返回一个`Promise`。我们到了:

```
const data = await doSomething()
    .catch(e => console.error('Error when doingSomething', e.message));
if (!data) { /* Bail out somehow */ } 
```

Enter fullscreen mode Exit fullscreen mode

就我个人而言，我对此百感交集。好点了吗？我们在混合技术吗？我想这大部分取决于我们在处理什么，所以你在这里。

请记住:

*   `await`不只是解析`Promise` s，而是*任何有`then`方法的*对象——a*thenable*(试试这个:`await {then() {console.log('Foo!')}}`)；
*   不仅如此，你还可以`await` *任何*对象，甚至是字符串或者`null`。

这意味着`then`或`catch`可能没有被定义，或者不是你所认为的那样。(还要记住，`.catch(f)`是`.then(null, f)`的糖，所以定义一个名称只需要后者。)

## 隐藏并行

如何一次解决多个并行(或者更好，并发)承诺？我们一直依赖`Promise.all` :

```
Promise.all([ doSomething(), doSomethingElse() ]).then(...);

// or in terms of await:
await Promise.all([ doSomething(), doSomethingElse() ]); 
```

Enter fullscreen mode Exit fullscreen mode

但是科里豪斯最近给出了这个建议:

> ![Cory House 🏠 profile image](img/b6f41d5c274123b20530e2595bf37da7.png)科里的房子🏠@ housecor![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)提示:使用 async/await，可以并行运行多个异步操作。怎么会？将 await 语句放在同一…[twitter.com/i/web/status/9…](https://t.co/ewRwzVKMwv)2017 年 11 月 13 日下午 16:20[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=930108010558640128)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=930108010558640128)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=930108010558640128)

因此，没有也有可能解决并发承诺*:* 

```
const a = doSomething();
const b = doSomethingElse();
// Just like await Promise.all([a, b])
await a, await b; 
```

Enter fullscreen mode Exit fullscreen mode

这里的诀窍在于，这些承诺在被`await`兑现之前已经*被*发起了。直接等待函数调用，而不是等待`a`和`b`会导致串行执行。

我在这里的建议是:警惕这些可能的并发问题；不要“自作聪明”地试图利用这一点。使用`Promise.all`在可读性方面要清晰得多。

## 不仅仅是糖

你可能听说过`async` / `await`就像 JavaScript 的许多其他新特性一样，只是你已经可以用经典的 ES5 JavaScript 做的一些事情的*语法糖*。这是*大部分*正确，但是，就像许多其他情况(类，箭头函数，等等。)，还有更多。

正如马蒂亚斯·拜恩斯[最近指出的](https://mathiasbynens.be/notes/async-stack-traces)，JS 引擎必须做大量的工作才能从`Promise`链中获得一个像样的堆栈跟踪，所以使用`async` / `await`无疑是更好的选择。

问题是我们不能随心所欲地使用它。我们仍然必须支持不支持新语法的旧浏览器，如 IE 或 Node 6.x。但是我们也不要忽略了 UC 和三星互联网这样的浏览器[也不支持](https://caniuse.com/#feat=async-functions)！最后，我们将不得不把它全部转移，并且还会这样做一段时间。

**更新(2018 年 3 月):**三星互联网和 UC 浏览器现在都支持`async` / `await`，但要提防老版本。

## 结论

我不知道你的，但我对 transpiled `async`函数的经验是...到目前为止还不太理想。看起来 Chrome 在处理 sourcemaps 时有一些错误，或者它们没有被很好地定义，但无论如何。

我用`async` / `await`吗？是的，当然，但是我认为由于所有提到的问题，我没有像我希望的那样经常使用它。这肯定是未来，但这是一个必须持保留态度的未来。

你对`async` / `await`有什么体验？*