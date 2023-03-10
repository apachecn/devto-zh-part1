# 纯函数，以及我喜欢它们的原因。

> 原文：<https://dev.to/nimmo/pure-functions-and-why-i-like-them>

纯函数并不新鲜。这绝不是一个新概念，当然也不是第一篇关于它们的文章。但是纯函数的好处值得经常大声重申，因为它们让你的生活变得更好。它们是自包含的，它们减少认知负荷，增加可测试性，导致更少的错误，并且本质上是可重用的。

在继续阅读之前，花点时间考虑一下以下函数的共同点。

```
const isOverLimit = x => x > limit
const multiply = x => x * config.requiredMultiple
const getItem = index => store[index]
const spaceAvailable = date => schedule[date].attendees < limitPerDay 
```

# 预见性

没有一个示例函数因为任何拉伸而变得复杂，但是这些示例的一个共同点是，你不能看着它们就知道它们的返回值是什么。您可以看到`isOverLimit`将返回 true 或 false，并且您可以推断该函数的目的是发现提供的值是否超过了系统强加的限制，但是您知道如果您用`isOverLimit(9000)`调用它，它是否会返回`true`吗？你必须找出`limit`对此指的是什么，这不必要地增加了你的认知负荷，并让你*在你的代码库中寻找其他地方来理解你最初在寻找的东西*；至少从我的经验来看，太多这样的事情会导致同样程度的分心和沮丧。

考虑这个选择:

```
const isOverLimit = (x, limit = 100) => x > limit 
```

现在你可以看看这个函数，看看它在任何情况下都会返回什么。可以看到`isOverLimit(9000)`会是`true`，`isOverLimit(9000, 9001)`会是`false`。

# 可重用性

再想想我原来的`isOverLimit`函数。想象一下，我的产品负责人有一天找到我，说我们公司正在为我们的产品增加一个新的“黄金”会员级别，有自己的特殊限制`1000`。在我的原始代码中，也许我会有`const isOverGoldLimit = x => x > goldLimit`，我会在某处维护`limit`和`goldLimit`。对于每一个引入的新成员级别，我都会继续编写相同的函数，对吗？

但是现在我的`isOverLimit`是纯净的，我可以重复使用它:

```
const isOverGoldLimit = x => isOverLimit(x, 1000) 
```

# 可测性

因此，示例`multiply`函数在我想象的系统中工作得很好，由于严格的业务需求，它必须将我们给它的东西乘以一个通过用户配置设置的数字，并且可以随时更新。由于另一个业务需求，我不允许知道这个数字是多少。由于第三个业务需求，我必须确保我有一个自动测试来证明这个功能工作正常。我该怎么做？不难发现，答案要么是“我不能”，要么是(如果你很大方的话)“有困难”。但是如果我把它重写为一个纯函数，就像我对`isOverLimit`所做的那样，它会是这样的:

```
const multiply = (x, y = config.requiredMultiple) => x * y 
```

因此，`config.requiredMultiple`仍然可以是之前的任何值，但关键是我可以很容易地编写一个测试来检查我的函数是否工作:`assert.equals(multiply(2, 4), 8)`

# 无副作用

纯函数不会对函数本身之外的任何值产生任何影响。考虑 JS 中`array.push`和`array.concat`的区别:

```
const updateItemsViewed = item => itemsViewed.push(item) 
```

太好了，这让我可以记录哪些项目已被查看。但是由于我在这里介绍的副作用，这个函数不会在每次用相同的输入调用时都给我相同的输出。例如:

```
let itemsViewed = ['item1', 'item2', item3']
console.log(updateItemsViewed('item4')) // ['item1', 'item2', 'item3', 'item4']
console.log(updateItemsViewed('item4')) // ['item1', 'item2', 'item3', 'item4', 'item4'] 
```

再次考虑这个函数的自动化测试——您应该立即看到的复杂情况是，测试本身将改变我的`itemsViewed`,所以当我第二次运行它时，它将第二次添加我的`test`项。您可能以前见过这种情况，其中自动化测试有一个“设置”或“拆除”来处理“重置”测试本身引入的任何副作用。但是如果你的函数首先是纯函数，你就不会有这个问题:

```
const itemsViewed = ['item1, 'item2', 'item3']
const updateItemsViewed = (item, itemsViewed = []) => itemsViewed.concat(item)
console.log(updateItemsViewed('item4', itemsViewed)) // ['item1', 'item2', 'item3', 'item4']
console.log(updateItemsViewed('item4', itemsViewed)) // ['item1', 'item2', 'item3', 'item4']

assert.deepEqual(updateItemsViewed('testItem'), ['testItem']) 
```

显然，这篇文章中的例子是为了演示我的观点而设计的，当然，你不可能拥有一个完全由纯函数组成的代码库，除非你编写的软件什么都不用做。但是说真的，尽可能地支持纯函数*，将应用程序的所有副作用保持在“边缘”,将来你会感谢自己的。任何需要看你代码的人也一样。:)*

 *# TL；速度三角形定位法(dead reckoning)

副作用最好尽可能避免，如果您严格要求使用纯函数，您将受益于更容易测试、更容易推理、更容易扩展和维护的代码库。如果你的函数可以在不使用返回值的情况下被调用，那么它们要么不是纯函数，要么什么也没做。无论哪种方式，你都不能重用它们或者为它们编写测试(很容易)，我强烈建议你应该考虑改变它们，如果它们不在你代码库的“边缘”的话。*