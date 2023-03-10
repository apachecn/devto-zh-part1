# 脑科学

> 原文：<https://dev.to/reflog/cerebraljs>

我想在这篇文章的前面加上以下免责声明:

> 我不是 Redux 的粉丝。它成为 React 应用程序状态管理的事实上的标准，似乎对许多人都很有效，但我发现它非常冗长，很难使用。

好的。既然已经解决了，让我们看看当今世界上还有什么可以帮助我们维护我们的应用程序状态**和**保持我们的理智。

我将要讨论的项目叫做[大脑](http://cerebraljs.com/docs/get_started/)，它是由克里斯蒂安·阿尔佛尼、阿列克谢·古里亚诺夫和许多其他人创建的，专门解决 Flux 和 Redux 的缺点。

我强烈推荐阅读 Christian 的[介绍文章](http://www.christianalfoni.com/articles/2017_03_19_Cerebral-2)到 main，以了解框架之间的主要差异。

在这篇文章中，我将通过比较使用 Redux 编写的基本反例和 brain 中的反例来对 brain 做一个简单的介绍。

在接下来的帖子中，我将开始介绍更先进的概念，这将是事情变得真正有趣的地方:)

## Redux 计数器

一个简单的 Redux 应用程序包括:

### 进入点

```
 import React from 'react'
import { render } from 'react-dom'
import { Provider } from 'react-redux'
import { createStore } from 'redux'
import counterApp from './reducer'
import Counter from './Counter'

let store = createStore(counterApp)

render(
  <Provider store={store}>
    <Counter />
  </Provider>,
  document.getElementById('root')
) 
```

Enter fullscreen mode Exit fullscreen mode

### 减速器

```
 export default (state = 0, action) => {
  switch (action.type) {
    case 'INCREASE':
      return state + 1
    case 'DECREASE':
      return state - 1
    default:
      return state
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 主要成分

```
 import React, { PropTypes } from 'react'
import { connect } from 'react-redux'
import { increase, decrease } from './actions'

const mapStateToProps = (state) => {
  return {
    count: state
  }
}

const mapDispatchToProps = (dispatch) => {
  return {
    onIncrease: () => {
      dispatch(increase())
    },
    onDecrease: () => {
      dispatch(decrease())
    }
  }
}

const Counter = ({ onIncrease, onDecrease, count }) => (
  <div>
    <button onClick={onIncrease}>+</button>
    {count}
    <button onClick={onDecrease}>-</button>
  </div> )

Counter.propTypes = {
  onIncrease: PropTypes.func.isRequired,
  onDecrease: PropTypes.bool.isRequired,
  count: PropTypes.string.isRequired
}

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(Counter) 
```

Enter fullscreen mode Exit fullscreen mode

### 动作

```
 export const increase = () => {
  return {
    type: 'INCREASE'
  }
}

export const decrease = () => {
  return {
    type: 'DECREASE'
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*它的工作方式如下*:你分别定义你的动作，然后在减速器中定义对这些动作的‘反应’，即状态将如何受到影响。然后将组件连接到状态。

这是在 [WebpackBin](https://www.webpackbin.com/bins/-KiQ3myWe7a1wO8he60v) 上的完整项目

## 大脑计数器

一个简单的大脑应用程序包括:

### 进入点

```
 import React from 'react'
import {render} from 'react-dom'
import {Container} from 'cerebral/react'
import controller from './controller'
import App from './App'

render((
  <Container controller={controller}>
    <App />
  </Container> ), document.querySelector('#app')) 
```

Enter fullscreen mode Exit fullscreen mode

### 控制器

```
 import {Controller} from 'cerebral'
import {set} from 'cerebral/operators'
import {state, string} from 'cerebral/tags'

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

export default controller 
```

Enter fullscreen mode Exit fullscreen mode

### 主要成分

```
 import React from 'react'
import {connect} from 'cerebral/react'
import {state, signal} from 'cerebral/tags'
export default connect({
  count: state`count`,
  onIncrease: signal`onIncrease`,
  onDecrease: signal`onDecrease`
},
function App ({ onIncrease, onDecrease, count }) {
  return (
   <div>
    <button onClick={() => onIncrease()}>+</button>
    {count}
    <button onClick={() => onDecrease()}>-</button>
  </div>
  )
}) 
```

Enter fullscreen mode Exit fullscreen mode

*它的工作方式如下*:你定义一个控制器，它包含一个状态和一个由它处理的信号列表。然后，您将组件连接到特定的状态元素和信号，并直接使用它们。

这是在 [WebpackBin](https://www.webpackbin.com/bins/-KiQ2BBcrcJ_gYnThQSp) 上的完整项目

如您所见，这里有很多不同之处:

1.  您不需要预先定义动作。
2.  没有“弦乐”魔术
3.  代码不那么冗长

你在这里看到的仅仅是 iseberg 的绝对尖端。大脑提供了**那么多**！我希望在接下来的文章中能涉及到这一切。