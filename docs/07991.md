# 大脑-第 4 部分，标签和操作符

> 原文：<https://dev.to/reflog/cerebral---part-4-tags-and-operators>

在本教程的前一部分，我已经讨论了如何使用**动作**更新应用程序状态。

我们实现了一个简单的函数，它使用 state 的`get`和`set`方法来改变 count 变量。

```
function changeCounter({props, state}){
  state.set("count", state.get("count") + props.param);
} 
```

Enter fullscreen mode Exit fullscreen mode

但是对每个状态变量重复这样做是非常多余的，我一直在说，大脑帮助我们编写更干净、更干燥的代码。

假设我们向状态添加了另一个变量来跟踪先前的计数器值。我们需要写另一个动作来更新它吗？没有。对此有一个非常干净的解决方案。

# 标签和运算符

由于对状态的许多操作是常见的，例如更新、删除、拼接、检查条件等，brain 提供了一组*操作符*来简化您的代码。

让我们看看如何使用它们。

我们将添加一个名为“prevCount”的状态变量，并在每次操作之前将“Count”复制到其中。

下面是如何完成的(只显示变化):

```
import {set} from 'cerebral/operators'
import {state} from 'cerebral/tags'

...

state: {
    prevCount: 0,
    count: 0
  },
  signals: {
     changeCounter: 
     [
       set(state`prevCount`, state`count`),
       changeCounter, 
       logAction
     ]
  } 
```

Enter fullscreen mode Exit fullscreen mode

让我们来分解一下我们在这里看到的内容:

1.  没有编写新的函数

2.  我们导入**设置**操作符和**状态**标记

3.  我们将**设置**添加到我们的信号链中，并使用**状态**标签来 a)获取当前计数 b)将其设置为 prevCount

那不是很好很干净吗？

(完整代码[此处](https://www.webpackbin.com/bins/-Kip6DwFIyUtghbue9QH))

任何阅读这个文件的人都会立即明白发生了什么，因为代码读起来像英语:

> 当触发信号 changeCounter 时，执行以下操作:将 state prevCount 设置为 state count 的值，然后更改 Counter 并调用 logAction。

这就是带有操作符的动作链的伟大之处。

这仅仅是表面现象。

下面是一些使用运算符和标签可以做的事情的例子:

## 更新列表

```
import {merge, push, pop} from 'cerebral/operators'
import {state, props} from 'cerebral/tags'

export default [
  merge(state`some.object`, props`newObj`),
  push(state`some.list`, props`newItem`),
  pop(state`some.otherList`)
] 
```

Enter fullscreen mode Exit fullscreen mode

## 去抖查询

```
import {set, debounce} from 'cerebral/operators'
import {state, props} from 'cerebral/tags'
import makeQueryRequest from '../chains/makeQueryRequest'

export default [
  set(state`query`, props`query`),
  debounce(500), {
    continue: makeQueryRequest,
    discard: []
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

## 执行条件执行

```
import {when, equals} from 'cerebral/operators'
import {state, props} from 'cerebral/tags'

export default [
  when(state`app.isAwesome`), {
    true: [],
    false: []
  },
  equals(state`user.role`), {
    admin: [],
    user: [],
    otherwise: []
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

从本质上讲，操作符实际上是一个“动作工厂”，也就是说，它是一个返回动作的函数。因此，您可以轻松地编写自己的操作符来清楚地表达您的意图，并使您的代码变得干净。

有关内置操作符的完整列表，请参见:[http://cerebraljs.com/docs/api/operators.html](http://cerebraljs.com/docs/api/operators.html)