# ES6 中的设置-快速指南

> 原文：<https://dev.to/mildrenben/sets-in-es6---a-quick-guide-3mmk>

## 概观

集合是 ES6 中的新数据类型。

它们类似于数组，但有一个主要区别，每个值都必须是唯一的。他们有一个与[地图](https://dev.to/mildrenben/maps-in-es6---a-quick-guide-35pk)非常相似的 API。它们比地图简单一点，所以不要担心！

集合如您所料被实例化:要么是空的，要么有一个 iterable。

```
const foo = new Set()

const foo1 = new Set([1,2,3,4,5]) 
```

您可以使用`.add()`方法添加到集合中。当它返回`this`时，你可以链接它们。

```
Set.prototype.add(value) : this

foo.add('hello')

foo.add('hey there')
   .add('yo') 
```

你也可以用`.has()`方法检查一个值是否存在。

```
Set.prototype.add(value) : boolean

foo.has('yo') // true

foo.has('greetings') // false 
```

你可能已经注意到这个 API 与现在的地图相比有一个模式，移除项目与`.delete()`和`.clear()`方法没有什么不同。`.remove()`根据值是否存在返回一个布尔值。

```
Set.prototype.delete(value) : boolean

foo.remove('yo') // true

foo.remove('greetings') // false

Set.prototype.clear() : undefined

foo.clear() // undefined 
```

静态属性`.size`返回集合中的项目数量。

```
Set.prototype.size : number

const bar = new Set([1,2,3,4,5])

bar.size 
```

### 设置 API 摘要

还有一些其他的迭代方法，我们将在下一节中介绍，但是这些是基本的。

```
const foo = new Set()

foo.add(key, value) 

foo.get(key)

foo.has(key)

foo.delete(key)

foo.clear()

foo.size 
```

## 使用

### 特殊雪花

还记得数组和集合的巨大区别吗？在一个集合中，一个值不能出现多次。所以每个值肯定是唯一的。这立刻有了一些奇妙的用例。我知道我以前肯定接触过 Lodash 的`_.uniq`功能，现在你不需要了。

```
const myArray = [1,1,2,2,3,3]

const mySet = new Set(myArray)

const uniqueArray = [...mySet] 
```

这是非常强大的，尽管数组最好有一个内置的方法来实现这个功能。(补充说明:`...`和`Array.from`是最慢的方法，而`for...of`是现代浏览器中最快的。这是一个真正的耻辱，因为前两个要干净得多。[来源](https://jsperf.com/set-iterator-vs-foreach/4)

### 循环

不太明显的是，您不能使用数组索引方法`[n]`从集合中检索值。这是因为集合没有被索引。

使用集合中的值的唯一方式是通过迭代，因此它们以插入顺序存储。您可以使用`.forEach()`方法来迭代一个集合。

```
const foo = new Set(1,2,3,4)

Set.prototype.forEach(callback(val1, val2, Set), [thisArg]) : undefined

foo.forEach(i => console.log(i)) // 1, 2, 3, 4 
```

奇怪的是，传递给`.forEach()`的回调接受 3 个参数。前两个参数是一样的:值。我想他们复制了这个值，以符合 map 拥有的相同 API(其中有一个键和值)。第三个值是您正在迭代的当前集合。很多方法都有这个问题，我也不知道为什么(如果你知道为什么，请评论)。最后，您可以随意设置`this`的值。

集合还使用迭代器协议，该协议允许您使用带有`for...of`循环的集合。

```
for (let i of foo) {
  console.log(i) // 1, 2, 3, 4, undefined
} 
```

* * *

地图有一个`.keys()`、一个`.values()`和一个`.entries()`方法。集合没有键，所以它们没有一个`.keys()`方法，但是它们有另外两个。

```
Set.prototype.values() : Set iterator

const vals = foo.values() // SetIterator {1,2,3,4}

for (let val of vals) {
  console.log(val) // 1, 2, 3, 4
}

Set.prototype.entries() : Set iterator

const ents = foo.entries() // SetIterator {1,2,3,4}

for (let ent of ents) {
  console.log(ent) // [1, 1], [2, 2], [3, 3], [4, 4]
} 
```

* * *

### 我应该使用集合还是数组？

当我们就[地图和物体](https://dev.to/mildrenben/maps-in-es6---a-quick-guide-35pk)问一个类似的问题时，地图似乎经常是更好的选择，但这对他们来说是一个很好的利弊权衡。不幸的是，我认为这个答案是片面的。

数组在日常生活中几乎总是更有用。集合中只有唯一的项目肯定是有帮助的，我实在找不到比数组更好的东西了。当然，在某些情况下集合会更好，但是它们是如此的少，以至于我基本上每次都会使用数组。

也就是说，请务必阅读下面的详细信息和注意事项，因为在某些特定的情况下，Set 更具性能或更实用。

## 细节和注意事项

### 什么是平等？

像地图一样，集合使用一种叫做 [SameValueZero](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness) 的东西来确定一个集合是否有值。

SameValueZero 与===非常相似，但是有两个显著的区别:

*   `NaN`等于`NaN`
*   `+0`等于`-0`

```
NaN === NaN // false

const foo = new Set()

foo.add(NaN)
foo.size // 1

foo.add(NaN)
foo.size // 1 
```

### 使用数组方法

集合和数组之间的转换非常容易，在集合上使用数组方法也很容易！

```
const foo = new Set([2,1])

const sortedArray = [...foo].sort()

const sortedSet = new Set(sortedArray) // 1, 2 
```

或者甚至短路(但是稍微不太清楚)

```
const foo = new Set([2,1])

const sortedSet = new Set([...foo].sort()) // 1, 2 
```

### WeakSet

WeakSets 是一种独立的数据类型，但它们非常类似于集合。

这是完整的 WeakSet API:

```
WeakSet.prototype.add(value) : this
WeakSet.prototype.delete(value) : boolean
WeakSet.prototype.has(value) : boolean 
```

正如我们所看到的，这就像 Set API，但是去掉了一些东西。

值得注意的是`WeakSet.prototype.clear()`曾经是规范的一部分，但现在已经被删除了。虽然这在某些浏览器上可能行得通，但你不应该使用它，因为它很快就会不受支持。

弱集合和集合之间有两个基本区别:

*   只能将对象添加到弱集，不能添加基本体
*   弱点允许它们值被垃圾收集

让我们看看第一点的作用:没有原语。

```
const foo = new WeakSet()

foo.add(1) // TypeError: Invalid value used in weak set

foo.add('hello') // TypeError: Invalid value used in weak set

foo.add({}) // ✅ 
```

这很简单，但是如果没有很好地理解垃圾收集在现代浏览器中是如何工作的，第二点就有点难理解了(我很快会写一篇关于这方面的博客)。

只要浏览器认为对象是“可到达的”，它就会在内存中保存该对象。如果你要在一个集合中存储一个值，而这个值在其他地方变得不可访问，它在集合中仍然是可访问的，因此垃圾收集器不会将它从内存中移除。

```
const foo = new Set()

let someObj = { name: 'Ben' }

foo.add(someObj)

someObj = null

foo.size() // 1 
```

在上面的例子中，`foo`保存这个值，并停止对它进行垃圾收集。弱集合微弱地保留它们的值*，并允许垃圾收集器删除它们。*

```
const foo = new WeakSet()

let someObj = { name: 'Ben' }

foo.add(someObj)

someObj = null 
```

不幸的是，没有办法检查对象是否已经从`foo`中移除，但是可以肯定的是，一旦垃圾收集发生，它就会被移除。

WeakSets 的 API 如此之小，以至于我看不到它们的日常用例。可能有一些非常特殊的情况，如标记某些项目。假设您需要知道是否标记了之前迭代过的项目。您可以向对象添加另一个属性，或者向 WeakSet 添加该项。

```
const foo = new WeakSet()

const someArr = [
  { name: 'Ben' },
  { name: 'Zoe' },
  { name: 'Roman' }
]

for (let value of someArr) {
  // Flipping a coin
  if(Math.random > 0.5) {
    foo.add(value)
  }
}

// You can check later if that value got a heads or tails
foo.has(someArr[0]) 
```

假设该物品将来变得不可及，它也将从武器集中移除。

老实说，这一切对我来说都太牵强了。只是感觉我基本上不会去拿一套内衣。我很乐意有人来评论，让我知道一些 WeakSets 的用例。*