# 大脑-第 3 部分，信号和行动

> 原文：<https://dev.to/reflog/cerebral---part-3-signals-and-actions>

# 重构

在本帖中，我们将对之前的反例进行一点点重构。

我们来回忆一下之前主控制器的样子:

```
 import {Controller} from 'cerebral'

function increase ({state}) {
  state.set('count', state.get('count') + 1)
}

function decrease ({state}) {
  state.set('count', state.get('count') - 1)
}

const controller = Controller({
  state: {
    count: 0
  },
  signals: {
     onIncrease: [increase],
     onDecrease: [decrease]
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

让我们试着理解这里到底发生了什么。

a .在控制器内部我们定义了两个信号:onIncrease 和 onDecrease。
b .对于每个信号，我们引用一个函数来处理该信号
c。该函数(增加/减少)将接收一个名为*‘context’*的参数。它将包含各种有用的信息，但是因为我们从它那里需要的只是我们的*‘状态’*，我们通过应用`{state}`作为它的参数来进行 ES6 的‘去结构化’

现在，让我们稍微重构一下这段代码，因为*增加*和*减少*函数基本上有相同的代码。我们如何做到这一点？

这里有一个方法:我们给信号传递一个参数，这个参数会选择计数器变化的方向。这样，我们就可以在两种情况下使用相同的函数。

现在我们的代码看起来像这样:

```
 import {Controller} from 'cerebral'

function changeCounter({props, state}){
  state.set("count", state.get("count") + props.param);
}

const controller = Controller({
  state: {
    count: 0
  },
  signals: {
     changeCounter
  }
})

export default controller 
```

Enter fullscreen mode Exit fullscreen mode

这里有两件事需要注意:
a .我们现在给我们的处理程序析构两个参数:`{state, props}`。`props`参数是一个包含传递给信号
b 的所有参数的对象。我们不再需要两个信号了！

现在我们已经更改了控制器，下面是组件的外观:

```
 import React from 'react'
import {connect} from 'cerebral/react'
import {state, signal} from 'cerebral/tags'
export default connect({
  count: state`count`,
  changeCounter: signal`changeCounter`
},
function App ({ changeCounter, count }) {
  return (
   <div>
    <button onClick={() => changeCounter({param:1})}>+</button>
    {count}
    <button onClick={() => changeCounter({param:-1})}>-</button>
  </div>
  )
}) 
```

Enter fullscreen mode Exit fullscreen mode

漂亮又干净！

# 信号链

你有没有注意到，当我们定义信号处理程序时，它们最初是作为数组传递的？原因很简单，一个大脑的信号触发了一连串的行动。

这个令人敬畏的特性允许您将当某个信号被触发时需要一个接一个发生的几个动作组合在一起。

让我们来看一个小例子。

我将添加一个日志操作，它将在每次更改时向控制台写入计数器的新值。

```
 import {Controller} from 'cerebral'

function changeCounter({props, state}){
  state.set("count", state.get("count") + props.param);
}

function logAction({state}){
  console.log(`counter changed to ${state.get("count")}`)
}

const controller = Controller({
  state: {
    count: 0
  },
  signals: {
     changeCounter: 
     [
       changeCounter, 
       logAction
     ]
  }
})

export default controller 
```

Enter fullscreen mode Exit fullscreen mode

现在，每次你触发一个改变，你会看到一个漂亮的日志消息！

动作、信号和链的好处是它们在调试器中都是可见的。看一看:

[![DEBUGGER](img/6fba7c65dbfe1fef335eb101253be291.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xWOq1EZi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.reflog.me/uploads/2017/04/29/dbg5.png)

这是在 [WebpackBin](https://www.webpackbin.com/bins/-KitMr9dCRc49A28ha6F) 上的完整项目

在这个系列的下一部分，我将讨论下一个令人惊奇的大脑特征- ' **标签**。