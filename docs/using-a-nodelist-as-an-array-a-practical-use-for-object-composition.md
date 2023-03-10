# 使用节点列表作为数组:对象合成的一个实际应用

> 原文：<https://dev.to/vidjuheffex/using-a-nodelist-as-an-array-a-practical-use-for-object-composition>

# 使用 NodeList 作为数组

## 场景:

我使用 querySelectorAll()查询了一些标签，并收到了一个返回的节点列表。

## 问题:

节点列表就像数组(也就是说，它们有一个长度属性，通过括号中的索引来访问:NodeList[0])。地图，或者。过滤器，或者。一号桌前。

## 接近:

那里的选择多种多样。从循环遍历和填充数组到一些更聪明的 es6 选项，比如:

```
var elements = [... nodelist] 
```

Enter fullscreen mode Exit fullscreen mode

```
var elements = Array.from(nodelist) 
```

Enter fullscreen mode Exit fullscreen mode

然而，这些有一个问题...他们做得太好了。现在你有了一个数组而不是一个节点列表。当然，它拥有来自节点列表的所有数据，但它不再将自己标识为节点列表。
**这有什么问题？**
试试:

```
nodeElementInTheArray.compareDocumentPosition(anotherNodeElementInTheArray) 
```

Enter fullscreen mode Exit fullscreen mode

这将出错，因为该参数不是真正的 NodeListItem。

### 让我们重新定义我们的需求

我们不需要我们的节点列表是一个数组，我们只需要那些来自数组的属性。这是物体构成的完美场所。

## 解

```
Object.assign(*NODELIST*, Object.Array) 
```

Enter fullscreen mode Exit fullscreen mode

我们的节点列表仍然是一个节点列表，它获得了我们需要的那些数组特征，同时没有修改它的原型。我在任何地方都没有看到这个解决方案，可能是因为它从来都不是人们所要求的。所以不要问，我如何让 A 变成 B，而是问，我如何让 A 表现得像 B，答案很可能是“对象组合”