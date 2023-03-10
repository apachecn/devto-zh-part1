# ES6 中的地图-快速指南

> 原文：<https://dev.to/mildrenben/maps-in-es6---a-quick-guide-35pk>

## 概观

在文章中，地图和布景经常被混为一谈。它们都是新的 ES6 集合类型，具有相似的接口，但相似之处仅限于此。所以有两篇独立的文章来讨论它们是有意义的。

地图就像数组和对象的私生子，有着漂亮而理智的界面。

您可以像这样实例化一个:

```
const foo = new Map() 
```

在我上一篇关于[符号](https://dev.to/mildrenben/symbols-in-es6---a-quick-guide-dhm)的文章中，我们研究了对象如何使用字符串或符号作为键。井图可以使用任何类型的数据作为关键字。使用`.set()`方法添加键值对。

```
Map.prototype.set(key, value) : this

// as `this` is returned, we can chain the set methods
foo.set(1, 'one')
   .set(null, 'nothing')

const bar = { name: 'Ben' }
foo.set(bar, { age: 25 }) 
```

创建地图时，还可以向构造函数传递一个 2D 可迭代对象。

```
const foo = new Map([
  [undefined, 'hello'],
  [null, 'nada']
]) 
```

使用`.get()`方法从地图中检索项目。

```
Map.prototype.get(key) : any

foo.get(undefined) // 'hello' 
```

与对象不同(但更像数组)，地图有一个方便的`.size`属性。

```
const foo = new Map([
  [1, 1],
  [2, 2]
])

Map.prototype.size : number

foo.size // 2 
```

映射检查对对象的引用是否相等，因此使用对象文字是一个坏主意，因为您将无法检索值。

```
const foo = new Map()

foo.set({}, `you'll never catch me`)

foo.get({}) // undefined 
```

地图提供了许多其他有用的方法。

```
const zoe = { name: 'Zoe' }
const foo = new Map([
  ['hey', 0],
  [9, 'nine'],
  [zoe, { age: 23 }]
])

// .has checks if the collection contains a key
Map.proptype.has(key) : boolean
foo.has(9) // true
foo.has(5) // false

// .delete simply deletes an item
Map.prototype.delete(key) : boolean
foo.size // 3
foo.delete(5) // false
foo.size // 3
foo.delete(9) // true
foo.size // 2

// .clear deletes all values
Map.prototype.clear() : undefined
foo.clear() // undefined
foo.size // 0 
```

### 地图 API 摘要

```
const foo = new Map()

foo.set(key, value) 

foo.get(key)

foo.has(key)

foo.delete(key)

foo.clear()

foo.size 
```

## 使用

与对象相比，地图的一个巨大优势是你可以迭代它们。它们被构建为使用像`.forEach`这样的内置方法和用于`for..of`循环的迭代器协议进行迭代。

它们也保持它们的顺序，不像对象，更像数组。所以你可以确定一切都是按正确的顺序进行的。

```
const foo = new Map([
  [1, 'first'],
  [2, 'second'],
  [3, 'third']
])

Map.prototype.forEach(callback(value, key, map), [thisArg]) : undefined

foo.forEach((val, key) => console.log(val, key)) 
// first 1
// second 2
// third 3 
```

`for..of`循环返回每个项目(包括键和值)，而不仅仅是您所期望的每个值。

您可以使用析构来分隔这些值。

```
for(let [key, val] of foo) {
  console.log(key, val)
}
// 1 'first'
// 2 'second'
// 3 'third' 
```

就像对象一样，我们可以访问`.keys()`、`.values()`和`.entries()`。

```
Map.prototype.keys() : Map iterator
const keys = foo.keys() // 1, 2, 3

Map.prototype.values() : Map iterator
const values = foo.values() // 'first', 'second', 'third'

Map.prototype.entries() : Map iterator
const entries = foo.entries() // [1, 'first'], [2, 'second'], [3, 'third'] 
```

这些方法都返回迭代器对象。因为它们都符合迭代器协议，所以它们可以和`for..of`一起使用，就像一个生成器或者使用`...`扩展操作符。

```
const keys = foo.keys()
for(let key of keys) {
  console.log(key)
}
// 1
// 2
// 3

const entries = foo.entries()
entries.next() // { value: [1, 'first'], done: false }
entries.next() // { value: [2, 'second'], done: false }
entries.next() // { value: [3, 'third'], done: false }
entries.next() // { value: undefined, done: true }

const values = foo.values()
console.log(...values) // 'first', 'second', 'third' 
```

上面所有的迭代方法都是一种更全面的方法，通过丰富的方法和一致的顺序来迭代键值存储；比在物体上要好得多。

* * *

地图的一个很好的用例是向现有对象添加元数据或向不可变对象附加一些额外数据的潜力。符号对于这个也很好，[见我之前的文章](https://dev.to/mildrenben/symbols-in-es6---a-quick-guide-dhm)。

```
const dataObjects = [
  { name: 'Ben', age: 25 },
  { name: 'Zoe', age: 24 },
  { name: 'Roman', age: 50 },
]

const mapObjects = new Map()
dataObjects.forEach(val => mapObjects.set(val, { created: new Date() }))

console.log(mapObjects.get(dataObjects[0])) // [{ name: 'Ben', age: 25 }, { created: Sun Dec 17 2017 11:57:52 GMT+0000 (GMT) }] 
```

* * *

### 我应该使用地图还是对象？

在看到地图所能做的一切后，你可能会想:“我还会需要使用普通的旧物件吗？”

让我们来看看各自的优缺点:

**优点**

*   已订购。它们像数组一样保持有序，并且有许多迭代方法。
*   对象(或任何其他类型)作为键。将对象作为键是非常强大的。

**缺点**

*   丢失了[很多对象方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/prototype)。大多数很少使用，但有些很方便。
*   没有快速点符号，这意味着没有析构。

所以，如果你需要迭代你的对象或者你想要不是字符串的键，你唯一的选择就是映射。如果您知道对象的形状，并且希望它是静态的(不添加或删除属性)，那么对象可能是最好的。

## 细节和注意事项

### 什么是平等？

地图使用名为 [SameValueZero](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness) 的东西来确定地图是否有键。

SameValueZero 与===非常相似，但是有两个显著的区别:

*   `NaN`等于`NaN`
*   `+0`等于`-0`

```
NaN === NaN // false

const foo = new Map()

foo.set(NaN, 'hello')
foo.get(NaN) // 'hello' 
```

### 不存在的关键点

当查找一个不存在的键时，返回`undefined`。如果您可能期望返回值为`undefined`，那么您需要意识到这一点。

```
new Map().get('notAKey') // undefined 
```

### 从对象构建

对象有一个方便的`.entries()`方法，它返回地图所需的 2D 数组结构。

```
const foo = { name: 'Ben', age: 25 }
const entries = Object.entries(foo) // [ ['name', 'Ben'], ['age', 25] ]
const foo = new Map(entries) 
```

### Weakmaps

尽管在细节和警告部分，WeakMaps 不是微不足道的！(我只是觉得这是放置它们的最佳位置)

WeakMaps 是一种独立于地图的数据类型。就像它们的非弱对应物一样，除了它们允许 Javascript 在需要时从内存中移除它们的键，并且键*必须是对象。*

他们有一个与地图非常相似的 api，只是去掉了一些方法。这些是 WeakMaps 拥有的所有方法:

```
WeakMap.prototype.get(key) : this
WeakMap.prototype.set(key, value) : this
WeakMap.prototype.has(key) : boolean
WeakMap.prototype.delete(key, value) : boolean 
```

简而言之，WeakMaps 基本上是允许自己的对象键被垃圾收集的地图。这有助于防止内存泄漏。

如果一个映射有一个对象作为键，而这个对象被破坏了，映射仍然保留这个对象作为键，它将留在内存中，不会被垃圾收集，因为它仍然是“可到达的”(不要担心，我很快就会写一篇关于垃圾收集如何工作的文章！).

```
const foo = new Map()

let bar = { name: 'Ben' }

foo.set(bar, { age: 25 })

foo.get(bar) // { age: 25 }

bar = null

foo.entries() // [ [{ name: 'Ben' }, { age: 25 }] ] 
```

相反，如果 WeakMap 将一个已删除的对象作为键，WeakMap 允许垃圾收集器删除该键及其相关值。

```
const foo = new WeakMap()

// You can only use objects as keys, no primitives
foo.set('primitive', 1) // TypeError: Invalid value used as weak map key

let bar = { name: 'Ben' }

foo.set(bar, { age: 25 })

foo.get(bar) // { age: 25 }

bar = null

// If there are no other reference to bar, it is removed as a key from foo 
```

现在，你可能想知道为什么我没有在上面的例子中显示 WeakMap 不保留`bar`键，因为我不能！

武器没有检查的工具，例如`.size`、`.entries()`、`.keys()`或`.values()`。这个限制有一个很好的理由:展示它对*来说是不安全的。*

Javascript 垃圾在不同的时间收集，这取决于正在执行的内容、当前操作的强度、要收集的量等等。每个 Javascript 引擎处理这些事情的方式也略有不同。因此，尽管我们知道 WeakMaps 键将被垃圾收集器删除，但我们不知道它将在何时运行。所以使用像`.size`这样的东西是不安全的，它可能会告诉我们，由于垃圾收集在后台运行，我们现在有 1 个项目，而下一个项目是 0。

#### 在哪里使用 WeakMaps 而不是地图

WeakMaps 不会有太多的用途，但在一些特殊情况下它们会派上用场。

一个潜在的用例是在状态管理中，您需要一个对象作为键。大多数较大的应用程序都有专门的状态管理库，如 Redux 或 Vuex。但也许对于一个较小的应用程序，如果你要推出自己的状态管理系统，使用 WeakMaps 可能会很方便。将对象存储为键，而且如果不再需要这些对象，还允许对它们进行垃圾收集，这是非常强大的。

* * *

也有可能使用 WeakMaps 获取私人数据。

```
const keepOut = new WeakMap()

class Person {
    constructor(name) {
    keepOut.set(this, { name })
  }
  get name () {
    return keepOut.get(this)
  }
}

const me = new Person('Ben')

me.name // { name: 'Ben' } 
```

因为 Person 的实例作为键存储在 WeakMap 中，所以没有 Person 的实例就不能访问数据。

* * *

我在上面提到了使用地图向对象添加元数据或者简单地扩展无法扩展的对象。WeakMaps 对于 DOM 元素来说非常方便。

```
const myH1 = document.querySelector('h1')
const myImg = document.querySelector('img')

const domElems = new Map([
  [myH1, { created: new Date() }],
  [myImg, { someExtraData: 'foo' }]
]) 
```

现在你可以跟踪额外的数据和 DOM 元素，当不再需要 DOM 元素时，WeakMap 将允许对它们进行垃圾收集！