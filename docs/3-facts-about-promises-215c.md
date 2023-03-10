# 关于承诺的 3 个事实

> 原文：<https://dev.to/kayis/3-facts-about-promises-215c>

*[封面图片由 Carmella Fernando 在 Flickr 上拍摄，由我裁剪](https://www.flickr.com/photos/13923263@N07/)*

承诺是 JavaScript 开发人员的新宠，但我经常读到这样的话“难道它们不是更复杂的回调吗？!"。所以在所有这些关于承诺的帖子之后，拉蒂利，我想捐出我的 2 美分，希望它能帮助一些人写出更好的代码。

## 1。承诺不在乎你什么时候需要他们的数据

承诺的一个非常好的特点是，当你用`then`添加一个回叫时，他们不会在意。

你可以像这样写代码，它就能工作:

```
const wait = ms => new Promise(r => setTimeout(r, ms));

const waitTenMilliseconds = wait(10);

setTimeout(() =>
  waitTenMilliseconds.then(() => console.log("Hello!"))
, 1000); 
```

Enter fullscreen mode Exit fullscreen mode

第一个承诺`waitTenMilliseconds`将在添加回调的超时之前解决，但回调仍将被调用。

提示:有时候，当打电话给你的时候，承诺的实现会在*上有所不同。有些人把所有的回调放在事件循环的末尾，有些人在承诺已经被解析或同步计算的情况下立即调用它们。*

## 2。承诺使其他承诺变得平淡

通常，当阅读基于 promise 的代码时，您可以马上看出有人不知道这一点，但这是一个非常好的特性，它允许您将已经拥有的值与需要异步计算或获取的值混合在一起。

`then`有*一元*行为，它在回调中查看你返回的内容，如果是另一个承诺，它会等待，直到它解决，从其中解包值，并将其放入下一个回调。如果它只是一个常规值，它会立即将其放入下一个回调函数中。

```
Promise.resolve("ID_123")
.then(userId => fetch("/users/" + userId)) // returns a promise
.then(response => response.json()) // returns a promise
.then(json => `${json.firstName}  ${json.lastName}`) // returns a string
.then(name => console.log(name)) // returns undefined 
```

Enter fullscreen mode Exit fullscreen mode

正如我所说，混合也是允许的！

```
const myData = [
  0,
  123,
  fetch("/number/456").text(),
  fetch("/number/789").text(),
  999
];

Promise.all(myData)
.then(numbers => console.log(numbers.join(", "))); 
```

Enter fullscreen mode Exit fullscreen mode

`myData`数组包含值和承诺。`Promise`的静态`all`方法将查看每个值，将其添加到一个新数组中，并在添加它们之前等待承诺解析。这允许下一个`then`回调得到一个相同大小的数组，但是在数组中的所有承诺被解析后，所有承诺都被它们的值所替换。

## 3。承诺早餐吃错误

大多数时候，当你的承诺失败时，你会得到一个*未处理的承诺拒绝*错误，但是更多的时候，你会得到一个不抛出任何东西的结果，你会被一个不做任何事情，甚至不显示错误的代码卡住。有时是因为库返回的承诺没有实现对错误的`reject`调用。

对于*异步函数*，情况变得更糟，因为你再也看不到承诺了。你把一个`async`放在你的函数前面，你所有的错误都消失了，因为你的函数现在被包裹在一个承诺里面，吞下了你的错误。

所以用承诺中的`catch`或者通过在*异步函数*中使用`try-catch`来检查错误是非常重要的。

## 结论

承诺是 JavaScript 的一个很好的补充，但是它们不是没有代价的。我认为与简单的回调相比，它们允许你编写更结构化的代码，但是，就像每一个抽象一样，你必须理解它们，否则它们会咬你一口。

* * *

### 50。文章

我 2017 年的新年决心是每周写一篇文章，我几乎实现了。

有时候我生病了或者不想写东西，但是你们的评论和喜欢让我坚持下去。

*谢谢！*

此外，我要感谢 **dev.to** 的创造者，是他们让开发者编写起来变得简单！

我最近看到开发领域的 MVP 一个接一个地涌向平台，所以 2018 年平台如何变化将会很有趣！