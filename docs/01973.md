# 迭代协议的重要性

> 原文：<https://dev.to/nipher/the-importance-of-iteration-protocols-33g>

大家好，最近我发现自己和我的一个好朋友([马克西姆·伊万诺夫](http://maksimivanov.com/)，他写了一些非常有用的文章)谈论 javascript 中的迭代协议，在讨论过程中，我们对这些协议带来的语言改进感到非常高兴，我们也注意到了更多关于它的事情，因为这次讨论，我决定写这篇文章。

让我们开始吧，首先，让我列出我想谈论的话题:

*   [迭代协议？](#iteration-protocols)
*   [其真值](#its-true-value)
*   [对于..](#for-of)的
*   [创建自定义迭代器](https://nipher.io/the-importance-of-iteration-protocols/#creating-a-custom-iterator)
*   [内置可重复项](https://nipher.io/the-importance-of-iteration-protocols/#built-in-iterables)
*   [遍历对象](https://nipher.io/the-importance-of-iteration-protocols/#iterating-over-objects)
*   [其他语言](https://nipher.io/the-importance-of-iteration-protocols/#other-languages)
*   [结论](https://nipher.io/the-importance-of-iteration-protocols/#conclusion)

## 迭代协议？

好吧，如果你甚至不知道我在说什么，我会解释得很快，并展示一个例子。

在迭代协议中，我们有 Iterable 和 Iterator。

让我们分别来看看:

#### 可迭代

该协议简单地定义了一种指定对象迭代行为的方法。

那么举个例子，我们有一个 string 对象`'Hello World'`，这个对象本身会告诉它的迭代应该是怎样的。

击球手出局了吗

那么，让我们来看一个代码片段并解释它的代码，我相信它会更容易理解。

```
const message = 'Hello World'

console.log(message[Symbol.iterator])
// > ƒ [Symbol.iterator]() { [native code] }

console.log(message[Symbol.iterator]())
// > StringIterator {} 
```

Enter fullscreen mode Exit fullscreen mode

所以，wtf？

让我们过一遍:

*   创建一个字符串并将其分配给`message`常量
*   我们使用`message[Symbol.iterator]`来访问一个函数(它将返回一个迭代器)
*   我们调用函数，它返回一个`StringIterator`(实现迭代器协议)

这就是 iterable 协议，定义了`[Symbol.iterator]`，因此任何人都可以调用它并获取它的迭代器，迭代器可用于获取要迭代的值。

你可能已经注意到了，我们不需要实现/定义它，string 对象已经定义了一个`[Symbol.iterator]`，它来自它的原型(`String.prototype[Symbol.iterator]`)，并不是所有类型的对象都是这样，我们将在主题“内置 iterables”中详细讨论它。

#### 迭代器

这个协议基本上是一个获取序列值的接口。

如果你稍微想一想，这就是迭代的意义，从一些东西中顺序地获取值。你也会意识到我们通常想要迭代多少不同类型的对象:

数组、字符串、映射、集合、DOM 数据结构、流、对象...

现在，如果我们想从这些人那里得到序列值，我们可以使用迭代器协议来实现。

一个迭代器需要实现这个接口:

```
{
  next() {
    return { value: <Anything>, done: <Boolean> }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们再次使用一个字符串，这样我们可以看到这两个协议一起工作:

```
const message = 'Hello'

const messageIterator = message[Symbol.iterator]() // > StringIterator {}

console.log(messageIterator.next())
// > { value: 'H', done: false }
console.log(messageIterator.next())
// > { value: 'e', done: false }
console.log(messageIterator.next())
// > { value: 'l', done: false }
console.log(messageIterator.next())
// > { value: 'l', done: false }
console.log(messageIterator.next())
// > { value: 'o', done: false }
console.log(messageIterator.next())
// > { value: undefined, done: true } 
```

Enter fullscreen mode Exit fullscreen mode

嗯，我想这开始有点意义了，对吧？

让我们快速浏览一遍

*   我们定义字符串并从中获取迭代器的实例
*   我们开始从迭代器实例(我提到的接口)调用`next()`
*   由`next()`返回的每个值都是字符串中的一个字母
*   它从字符串中按从左到右的顺序返回字母
*   当没有剩余的字母时，我们得到`undefined`作为值，得到`true`作为完成(这意味着没有更多的值)

如果你仔细想想，这是一个非常简单的接口，但是它给 javascript 语言带来了很多价值。

## 它的真正价值

正如我之前所看到的，我们有很多想要迭代某种结构/对象的情况。

我们真的没有一个定义良好的接口来做这件事，导致了不同的迭代方式。

很多图书馆为我们解决了这个问题，`lodash`比如:

```
_.forEach('Hello', value => console.log(key))
// > 'H' 
// > 'e'
// > 'l'
// > 'l'
// > 'o'

_.forEach([1, 2], value => console.log(value))
// > 1 
// > 2

_.forEach({ 'a': 1, 'b': 2 }, (value, key) => console.log(key))
// > 'a' 
// > 'b' 
```

Enter fullscreen mode Exit fullscreen mode

正如你在上面看到的，一个单一的函数`.forEach(...)`可以处理任何类型的对象(字符串，数组，对象)。

但是是时候了，语言本身会改进这一点，所以我们不需要一个库来用编程语言执行这么简单的事情。

不要误解我的意思，我喜欢 lodash 之类的东西，它们在今天仍然非常有用和相关，它们自己可以使用迭代接口并从中受益，它们确实如此，想象一下现在实现它们的`_.forEach`方法比以前简单多了。

这是真正的价值，简单性、一致性和定义良好的模式的结合。

## 为..关于

那么，我们如何以通用的方式使用这些协议呢？

现在，我们有了`for (let value of <iterable>) { ... }`。

如你所见，它与我们习惯的`for`不同。

我们来看一些`for..of`的例子:

```
const message = 'Hello'

for (let letter of message) {
  console.log(letter)
}

// > H
// > e
// > l
// > l
// > o

const list = [1, 2, 3, 4, 5]

for (let i of list) {
  console.log(i)
}

// > 1
// > 2
// > 3
// > 4
// > 5

const person = new Map([['name', 'jonas'], ['age', 23]])

console.log(person)
// > Map { name → "Jonas", age → 23 }

for (let [key, value] of person) {
  console.log(`${key}:`, value)
}

// > name: Jonas
// > age: 23 
```

Enter fullscreen mode Exit fullscreen mode

`for..in`怎么样？

还是不一样。

`for..in`迭代不使用迭代协议，它迭代对象的可枚举属性，除非属性的名称是一个`Symbol`或者通过`Object.defineProperty`将`enumerable`设置为`false`来定义。

这也意味着它还会迭代它的原型属性(如果它们符合上面的描述)。

您可以通过在您的`for..in`块中添加一个条件`if (obj.hasOwnProperty(prop)) { ... }`来避免这种情况，这样它将只为实际实例的属性执行代码。

但是，如果您愿意，您可以避免使用`for..in`，并且像使用`for..in`一样使用带有`Object`实例的迭代协议(但是不需要条件)，确保在使用带有`Object`类型的`for..of`时使用静态方法之一，例如:`Object.entries`

我将在主题“对象迭代”中展示它的样子。

## 创建自定义迭代器

需要指出的一件有趣的事情是，这些协议并不局限于只在 javascript 引擎中实现，还可以创建一个自定义的协议。

我们来看一个例子？

```
function rangeOf(n) {
  let i = 1
  const range = {}

  range[Symbol.iterator] = () => ({
    next() {
      let [value, done] = (i <= n ? [i++, false] : [undefined, true])
      return { value, done }
    }
  })

  return range
}

for (let i of rangeOf(5)) {
  console.log(i)
}

// > 1
// > 2
// > 3
// > 4
// > 5 
```

Enter fullscreen mode Exit fullscreen mode

好，让我们再一次检查代码...

嘿，我希望你喜欢它，在我的网站上阅读这篇文章的其余部分，这样我也可以在那里获得一些访问和评论。

我努力重新设计它，让它对你来说超级棒:)

**访问这里** : [帖子链接](https://nipher.io/the-importance-of-iteration-protocols/)

谢谢大家！