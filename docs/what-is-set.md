# 什么是设置？

> 原文：<https://dev.to/zacanger/what-is-set>

*最初发布于[我的博客](http://blog.zacanger.com)。*

最近有人在一个 Slack 频道问他们如何清理下面的代码:

```
getNames (contacts) {
  let contacts_set = []

  contacts.forEach((contact) => {
    let firstName = get(contact, 'contactInfo.firstName')
    let lastName = get(contact, 'contactInfo.lastName')
    let fullName = `${firstName}  ${lastName}`

    if (contacts_set.includes(fullName)) {
      return
    } else {
      contacts_set.push(fullName)
    }

    set(this, 'contactsSet', contactsSet)
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

(注意，这里的`get`和`set`似乎是余烬特有的，或者是`_.get`类型的东西。我不认识恩伯，所以我不确定。)

我说，这个怎么样？

```
getNames: (contacts) =>
  [...new Set(
    contacts.map(({ contactInfo: { firstName, lastName }}) =>
      `${firstName}  ${lastName}`))] 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢这个版本，因为它简洁、易读(至少对我来说是这样？)、无突变和声明性的。

然后又有人问:

> 有人能说出上面提到的是什么吗？

这让我意识到许多人仍然没有使用 ES2015 中的许多好东西，所以我解释了一下。我是这么理解的。

Set 是一个新的(在 ES2015 中)可迭代内建函数(如 Array、String、TypedArray)。地图也是 2015 年新出的。集合对于数组就像映射对于对象一样。

对于实际应用来说，集合基本上是数组，但是是唯一的，并且有不同的方法。`add`、`has`、`delete`、`size`等等。MDN 上有更多关于[的信息。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set)

你可以传递给`Set`一个 iterable，这就是为什么我上面的东西可以工作(因为`new Set()`里面的`contacts.map...`产生了一个数组)。

在这种情况下，`Set`除了是`Set`之外没有被使用太多(所以它只保存唯一的值)。其他人在同一频道指出，如果你有一个非常大的收藏，这可能不是最好的主意，因为在这个过程中你会创造一个额外的收藏，这是完全正确的。

这里有一点关于 JS 的`Set`应该是什么样的背景。

下面看起来有点像这样:

```
class Set {
  constructor () {
    this.storage = []
  }
  add (a) {
    if (!this.storage.includes(a)) {
      this.storage.push(a)
    }
  }
  has (a) {
    return this.storage.includes(a)
  }
  remove (a) {
    this.storage.splice(this.storage.indexOf(a), 1)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是很多东西，但大多数情况下你可以把`Set`看作是一个类似于`Array`的东西，但它只包含独特的东西。

还有一个`WeakSet`只能装物体。就我个人而言，我还没有真正找到`WeakSet`和`WeakMap`的有效用例。

原来是相当有用的。绝对可以拿来玩玩！