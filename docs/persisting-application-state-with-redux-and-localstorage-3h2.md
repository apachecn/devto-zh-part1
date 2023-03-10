# 用 Redux 和 localStorage 保持应用程序状态

> 原文：<https://dev.to/arojunior/persisting-application-state-with-redux-and-localstorage-3h2>

[![](img/862ca9690f740433d0f23e02d3b9d2cf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--E9REjucK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AyiuStIm2N_qkZ34v4nwCVQ.png)

*注:原文是葡萄牙语，由谷歌翻译*

这个库允许您监控应用程序的状态([*Redux*](http://redux.js.org/)*store*)和[*replicate*](https://developer.mozilla.org/pt-BR/docs/Web/API/Window/Window.localStorage)in[*local storage*](https://developer.mozilla.org/pt-BR/docs/Web/API/Window/Window.localStorage)(浏览器)，从而允许页面在不丢失状态的情况下重新加载。

*   第一步是导入库并通知*商店*

```
import { createStore, combineReducers } from 'redux'
import storeSynchronize from 'redux-localstore'
import { Reducer1, Reducer2 } from './modules'

const combineReducer = combineReducers({  
 Reducer1,  
 Reducer2  
})

export const store = createStore(combineReducer)

storeSynchronize(store) 
```

Enter fullscreen mode Exit fullscreen mode

只有粗线，你的应用程序的整个状态将被复制到*浏览器*的本地存储器。其他行是 *Redux* 应用的默认设置。

*   第二步，您需要配置*还原器*，以便它们在应用程序启动/重新加载时从*本地存储器*收集这些数据。

```
import { defineState } from 'redux-localstore'

const defaultState = {
  data: null
}

const initialState = defineState(defaultState)('Reducer1')

export default (state = initialState, action) => {
  switch (action.type) {
    case 'ACTION1':
      return {
        ...state,
        data: action.payload
      }
    case 'ACTION2':
      return {
        ...state,
        data: null
      }
    default:
      return state
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

方法**define estate**检查在 *localStorage* 中是否有 *something* ，如果有，则将其作为初始状态……否则，使用默认状态集。很简单，不是吗？

唯一需要注意的细节是，作为第二个参数传递给*定义属性*的名称是在*组合减速器*中使用的*减速器*的名称。

设置基本上就是这些，你可以在:[https://github.com/arojunior/redux-localstore](https://github.com/arojunior/redux-localstore)找到这个库