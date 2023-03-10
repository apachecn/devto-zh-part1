# 在 TypeScript 中索引对象

> 原文：<https://dev.to/mapleleaf/indexing-objects-in-typescript-1cgi>

这是一个不时出现的话题，所以我觉得写一篇关于它的帖子会很有用。

假设您有这样的代码:

```
// an object defining how to display specific user statuses in the UI
const statusDisplays = {
  online: 'Online',
  offline: 'Offline',
  busy: 'Busy',
  dnd: 'Do Not Disturb',
}

// fetch the status of a user by their ID
const userStatus = getUserStatus(myUserID)

// get the displayed status text
const displayedStatus = statusDisplays[userStatus] 
```

Enter fullscreen mode Exit fullscreen mode

但是，在启用了严格模式的情况下，最后一行给出了一个有趣的错误:

> 元素隐式具有“any”类型，因为类型“{ online:string；离线:字符串；忙:字符串；dnd:字符串；“}”没有索引签名。

...如果你不知道发生了什么，这看起来完全是胡说八道。所以我们来试着分解一下。

## 指标签名

在 TypeScript 中，为了获取对象的索引，该对象的类型必须包含索引签名。索引签名通常用于定义用作字典的对象，就像我们这里的这个。索引签名类型如下:

```
type Dictionary = { [index: string]: string } 
```

Enter fullscreen mode Exit fullscreen mode

我们的对象被推断为类型`{ online: string; offline: string; busy: string; dnd: string; }`，并且不包含索引签名。

这样做的原因是为了防止由未知键索引一个对象而产生的运行时错误。想象一下，我们使用的 API 增加了一个新的状态“空闲”。当我们试图使用它时,`statusDisplays['idle']`会返回`undefined`和错误。或者更糟，默默失败。

## 直截了当的解决办法

要在 JS 中解决这个问题，首先检查键是否有效就足够了，TypeScript 通常支持这种检查。

```
if (userStatus in statusDisplays) {
  statusDisplays[userStatus]
} 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这仍然产生相同的错误消息，[，原因在这里讨论，并在其他类似的问题](https://github.com/Microsoft/TypeScript/pull/12253#issuecomment-263132208)。

这里有一种使用助手函数的方法:

```
// `PropertyKey` is short for "string | number | symbol"
// since an object key can be any of those types, our key can too
// in TS 3.0+, putting just "string" raises an error
function hasKey<O>(obj: O, key: PropertyKey): key is keyof O {
  return key in obj
} 
```

Enter fullscreen mode Exit fullscreen mode

```
if (hasKey(statusDisplays, userStatus)) {
  statusDisplays[userStatus] // works fine!
} 
```

Enter fullscreen mode Exit fullscreen mode

这使用了一些您可能不知道的有趣特性。即[泛型](https://www.typescriptlang.org/docs/handbook/generics.html)、 [`keyof`](https://www.typescriptlang.org/docs/handbook/advanced-types.html#index-types) 、[自定义类型守卫](https://www.typescriptlang.org/docs/handbook/advanced-types.html#user-defined-type-guards)。

我们使用泛型来推断传入的对象的形状，以便在返回类型中使用它。这里将`hasKey`的`O`参数推断为`{ online: string; offline: string; busy: string; dnd: string; }`。

`keyof`是 TypeScript 中的一个关键字，它接受给定的对象类型并返回其键的一个[联合类型](https://www.typescriptlang.org/docs/handbook/advanced-types.html#union-types)。这些是等价的:

```
type StatusKey = keyof { online: string; offline: string; busy: string; dnd: string; }
type StatusKey = 'online' | 'offline' | 'busy' | 'dnd' 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们在这里使用一个类型保护来说明，如果这个函数返回 true，那么对`key`的任何进一步使用都将是指定的类型。否则，它仍然只是一个字符串。

所有这些都是可行的，因为 TypeScript 允许我们索引任何对象，只要索引的类型是所有可能的键的联合，因此它知道该键是有效的。也许在未来，使用`key in obj`可以独立工作，但在此之前，助手功能已经足够好了。

如果您有任何问题或意见，特别是如果我遗漏了什么或有什么不清楚的，请随时在下面留下。感谢阅读！