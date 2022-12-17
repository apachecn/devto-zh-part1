# 解决工作申请代码难题

> 原文：<https://dev.to/bengreenberg/solving-a-job-application-code-challenge-30d>

正如我在之前的一篇文章中所讨论的，我最近从熨斗学校的在线沉浸式全栈训练营毕业。过去几周，我一直在努力寻找一份工作，这份工作包含我热爱的工作、我擅长的工作和我能产生有意义影响的工作这三个方面。令人欣慰的是，我发现这个行业充满了机会，可以为寻求改善人们生活的激动人心的努力做出贡献，无论是关于效率、沟通、财务规划还是许多其他领域。

面试过程中不可或缺的一部分就是向未来的雇主展示你的技能。面试过程的这一部分对最近的训练营毕业生来说可能是可怕的，特别是*文科程序员* ( *这个词是我为那些没有数学或非科学背景的人创造的*)。在本周的文科程序员编码概念部分，我们将分解一个真实工作申请中的代码挑战。

这是一个挑战:

> 对以下字符串中的字符进行排序:
> 
> `abcdefghijklmnopqrstuvwxyz_`
> 
> 按字符在以下文本中出现的次数(降序):
> 
> ...
> 
> 现在获取排序后的字符串，并删除所有在 _ 后面的字符。剩下的一个字就是答案。

为了简洁起见，我没有在上面的引用中包含很长的文本字符串。这是一个很长的文本字符串。

这个挑战并没有指定用哪种语言来解决这个问题，所以我们打算用 Javascript 来解决。为什么是 Javascript？它是一种非常受欢迎的语言，用于各种角色，熟练掌握它是应用程序中的资产。

我们要做的第一件事是创建一个新函数，它将`.reduce()`我们非常长的文本字符串。(等等，我们被赋予了一个`string`，而不是一个`array`，我们将如何在其上使用`.reduce()`？我们会到达那里的。)T4 是做什么的？根据 [MDN Web 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce?v=b)，`.reduce()`执行以下操作:

> reduce()方法对一个累加器和数组中的每个元素(从左到右)应用一个函数，将其缩减为一个值。

我们想使用`.reduce()`,只是因为它会计算出每个字符在一长串文本中出现的总次数。所以就这么办吧:

```
function findTheWord(array) {
  let newArray = array.reduce((total, char) => {
    if (total[char] === undefined) {
      total[char] = 0;
     }
    total[char] += 1
    return total
  }, {}); 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里做了什么？

首先，我们创建了一个新变量`newArray`来保存我们的`.reduce()`操作的结果。然后我们首先检查值是否是`undefined`，如果是，我们给它赋值 0。否则，每遇到一个字符，我们就加 1。最后，我们将`total`作为一个包含键值对的对象。

现在我们有了每个字母出现次数的对象列表，接下来我们做什么？好吧，挑战说它需要按照*降序*排列，因此让我们这么做:

```
...

let descendingOrder = Object.keys(newArray).sort((a, b) => newArray[b] - newArray[a]) 
```

Enter fullscreen mode Exit fullscreen mode

这里我们创建了一个名为`descendingOrder`的新变量，它将通过向按`newArray[b] - newArray[a]`排序的`.sort()`函数提供一个参数，按照降序组织我们的对象键(字符)的内容。

最后一步是`return`我们得到的只有之前和之前的字符，但不包括“_”字符。我们将用一个`.slice()`来指定我们想从哪里开始，想在哪里结束:

```
...

return descendingOrder.slice(0, descendingOrder.indexOf("_")).join(''); 
```

Enter fullscreen mode Exit fullscreen mode

在这个操作中，我们只从第一个索引点返回`descendingOrder`的值，直到到达“_”字符。`.join()`方法[将一个数组](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/join)的所有元素连接成一个字符串，我们在这里需要这样做，因为，如果你还记得的话，我们以某种方式将最初的长字符串转换成一个数组，以完成我们对它的操作。

我们的函数现在看起来像这样:

```
function findTheWord(array) {
  let newArray = array.reduce((total, char) => {
    if (total[char] === undefined) {
      total[char] = 0;
     }
    total[char] += 1
    return total
  }, {}); 
  let descendingOrder = Object.keys(newArray).sort((a, b) => newArray[b] - newArray[a])
  return descendingOrder.slice(0, descendingOrder.indexOf("_")).join('');
} 
```

Enter fullscreen mode Exit fullscreen mode

为了把我们的长字符串转换成一个数组，我们只需要在运行新函数之前先把它转换成一个数组，就像这样:

```
let array = Array.from(longString);
findTheWord(array);
// returns the word hidden in that long string of initial characters 
```

Enter fullscreen mode Exit fullscreen mode

我们解决这一应用挑战的方法演练到此结束。编码的伟大之处在于有很多方法可以完成任何事情。请在评论中分享你将如何应对这一挑战！