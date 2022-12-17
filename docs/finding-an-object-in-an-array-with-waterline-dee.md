# 在有水线的数组中查找对象

> 原文：<https://dev.to/prestonjlamb/finding-an-object-in-an-array-with-waterline-dee>

[Sails.js](https://sailsjs.com/) 是我最喜欢的节点框架之一。有许多内置的优势，使启动和运行快速，容易，没有痛苦。好处之一是它自带的 ORM/ODM，Waterline。有许多适配器允许您在应用程序中使用许多不同的数据库，而不需要重写数据库访问代码。这一次，虽然，当使用 Waterline 时，我有点卡住了，但这里是我如何得到我想要的数据。

假设我有一个这样的模型:

```
// MyModel
module.exports = {
    attributes: {
        field1: 'string',
        field2: 'string',
        field3: 'array'
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

而`field3`是一个对象数组。现在，我在数据库中查询结果，其中`field3`中的一个对象具有符合我们标准的属性。据我所知，Waterline 并没有提供一种开箱即用的方法来实现这一点。但是，它将允许您对复杂的场景使用特定于数据库的查询。(我非常乐意承认我可能误解了这一部分。然而，我将向您展示的查询确实有效。所以至少我得到了我想要的结果！)不管怎样，考虑到上面的场景，这里有一个有效的查询:

```
const results = await MyModel.find({ field3: { nestedAttr: searchParam } } ); 
```

Enter fullscreen mode Exit fullscreen mode

这将给出在数组`field3`中的对象，这些对象具有与你的`searchParam`相匹配的属性。这个查询在 Robomongo 中也可以工作，这意味着它比 Waterline 更特定于 mongo。但是在某些情况下，您(像我一样)可能需要使用特定于数据库的查询。