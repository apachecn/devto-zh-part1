# 了解 Redux 中间件并编写自定义中间件

> 原文：<https://dev.to/imwiss/understanding-redux-middleware-and-writing-custom-ones>

如果你使用 redux，你很可能已经使用过 Redux 中间件了——例如——`redux-thunk`、`redux-promise-middleware`、`redux-saga`或`redux-logger`。在大多数应用程序中，这些都是重要的中间件，但是我们中的一些人(包括我)认为这个概念是理所当然的，没有进一步挖掘。

也就是说，我最近不得不在一个大型应用程序中实现一个缓存解决方案，在做了一些研究并向我的一个同事请教之后(谢谢推荐！)，决定实现一个定制的中间件是最好的方法，主要是因为:

*   这有助于代码更加简洁
*   这使得代码更易于维护(考虑关注点的分离)
*   它将所有缓存逻辑集中在一个位置

在本文中，我将解释什么是 Redux 中间件，以及我是如何实现定制中间件的。

### 什么是中间件？

对于后端开发人员来说，Redux 中间件类似于 Express 或 ASP.NET 中的中间件。虽然这不是完全一样的东西，但它是相似的，代表了这个概念的一种很好的思考方式。

在 Redux 中，中间件用于在分派的动作到达 reducer 之前拦截它们。这意味着，当您对一个动作调用`dispatch`时，该动作在到达缩减器之前会经过一个(或多个)中间件——如果它甚至能到达那么远的话，但是稍后会到达更多。

[![This is what the Redux middleware flow looks like](img/618aec24b8c92c0e1b3187e4dd488778.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--zXenhLIr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://designingforscale.com/conteimg/2017/09/reduxMiddleware.png)

您可以对一个 Redux 存储应用多个中间件，这意味着动作在到达 Redux 之前必须通过所有的中间件。执行的顺序实际上就是你把中间件传递给商店的顺序。此外，在中间件的任何时候，您都可以选择停止转发动作，这将结束循环。

例如，在我的缓存中间件中，我首先检查*相同的动作*是否已经在进行中。如果是，我缓存最新的有效负载，并通过退出中间件来中断流。由于我没有调用`next`或者`dispatch`，动作流就不继续了。

### 为什么要用中间件？

如上所述，动作在到达 reducers 之前要经过中间件，这给了我们一个将逻辑或过滤器应用于所有动作的好方法。这意味着逻辑被集中在一个地方，而不是分散在不同的 reducers 中，如果出现 bug，我们可以很容易地确定在哪里进行调查，如果需要的话，我们可以把代码换出来。

受益于使用中间件的一些用例:

*   日志记录:每个动作都要经过这个中间件，因此我们可以记录它的类型和有效负载，以便进行调试或跟踪。
*   错误跟踪:如果任何异步操作返回一个错误，这个中间件可以显示一个通知。
*   缓存:对同一动作只调用一次 API，并缓存结果以备将来调用。
*   认证请求:对于 API 调用，在发出请求之前应用认证令牌。
*   这么多:)

### 编写中间件

要定义自己的中间件，需要用下面的签名写一个函数:
`store => next => action => result`

乍一看，这看起来非常令人困惑——我明白了——所以让我们把它分解一下:

*   `store`是将被传递到您的中间件的 Redux store 实例。
*   `next`是一个函数，当您想要继续流执行时，您需要用一个动作来调用它，这意味着将动作传递给下一个队列:要么是下面的中间件，要么是缩减器。
*   `action`是最初分派的动作，以便您可以访问它，基于动作应用逻辑，并最终使用`next`传递它。
*   `result`是用作调度调用结果的值。

最后，要将这个中间件应用到 Redux store 中，需要在通过`createStore()`创建 store 时调用`applyMiddleware`。这里有一个来自 Redux 官方文档的好例子:

```
import { createStore, combineReducers, applyMiddleware } from 'redux'

let todoApp = combineReducers(reducers)
let store = createStore(
  todoApp,
  // applyMiddleware() tells createStore() how to handle middleware
  applyMiddleware(logger, crashReporter)
) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，中间件`logger`将首先被调用，然后是中间件`crashReporter`，因为这是它们被传递给`applyMiddleware`的顺序。

### 缓存中间件

正如本文概述中提到的，我最近实现了一个缓存中间件来解决一个非常具体的问题。我知道现在已经有了一些缓存中间件，但是我需要一些小的、特定于手头问题的东西，所以我写了几行代码，而不是使用现有的库。

对于这个问题，我必须确保 WYSIWYG 编辑器在保存内容时只顺序调用后端。例如，如果在保存已经开始的时候自动保存开始了，那么在之前的调用完成之前，我不想将文本发送到后端。如果用户多次点击**保存**按钮，同样的概念也适用。

下面是我的中间件的样子:

```
export default function textUpdatesMiddleware () {
  return store => next => action => {
    if (action.type === UPDATE_TEXT) {
      // Check if the new text in the payload is different from what we already have in the store
      if (!shouldSaveText(action.payload, store)) return

      // Are we currently saving?
      // isUpdatingText is set to `true` in a reducer
      // A reducer listens to CACHE_TEXT_UPDATE and will store the payload into `pendingTextUpdate`
      // We only cache the latest content, not all of them
      if (store.getState().isUpdatingText) {
        return store.dispatch({
          type: CACHE_TEXT_UPDATE,
          payload: action.payload
        })
      } else {
        // This uses `redux-promise-middleware`
        return store.dispatch({
          type: UPDATE_TEXT,
          payload: {
            promise: http.patch(apiEndpoint, content)
          }
        })
      }
    }
    // This uses the `redux-promise-middleware` convention of _PENDING, _FULFILLED, _REJECTED
    if (action.type === UPDATE_TEXT_FULFILLED) {
      const pendingTextUpdate = store.getState().pendingTextUpdate
      // If we had a pending update
      if (pendingTextUpdate) {
        // A reducer listens to UNCACHE_TEXT_UPDATE and will clear `pendingTextUpdate`
        store.dispatch({ type: UNCACHE_TEXT_UPDATE })
        // Allow the fulfilled action to continue on to the reducers
        next(action)
        // Dispatch the update with the cached content
        return store.dispatch({
          type: UPDATE_TEXT,
          payload: pendingTextUpdate
        })
      }
    }
    // Nothing to do here - keep calm and carry on
    next(action)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

基于上面的代码，值得注意的是，当您从中间件内部调用`store.dispatch`时，Redux 应用了一些魔法，动作将再次穿过所有的中间件，包括调度它的当前中间件。然而，当您调用`next`时，动作会移动到流程中的下一个中间件。

### 结论

这个中间件解决了我遇到的一个具体问题，但是我们也可以让它更通用，这样它就可以将相同的概念应用于所有的(或子集)动作。在这一点上，我没有任何必要使它通用，所以我不想过度设计它，但值得注意的是，它肯定是可行的。

如果我没有在中间件中应用这个逻辑，我将不得不验证一个 API 调用当前没有从一个 reducer 中进行，然后从 reducer 中调度调用来缓存内容，并监听来自 reducer 的`FULFILLED`动作或 http 调用上的`then`，然后重新调度动作。这很快就会变得很乱，如果我们需要使它更通用的话，就不能很好地扩展。

我希望这是对中间件的一个很好的介绍，并且如果您需要编写自己的自定义中间件，它涵盖了足够的基础知识，可以帮助您入门。

我的最后一条建议是，研究和讨论是非常有价值的。我很高兴我决定不采用我最初的(糟糕的)方法，因为感觉有些不对劲，我做了更多的研究，与同事讨论，最终决定使用中间件，因为最终结果是一个更好的解决方案。

### 信用

*   关于中间件的官方 Redux 文档部分。
*   [你没有充分使用 Redux 中间件](%5BYou%20Aren%E2%80%99t%20Using%20Redux%20Middleware%20Enough%20%E2%80%93%20Jacob%20Parker%20%E2%80%93%20Medium%5D(https://medium.com/@jacobp100/you-arent-using-redux-middleware-enough-94ffe991e6))。

*本帖最初发表于[为规模](https://designingforscale.com/?utm_source=redux-middleware&utm_medium=guest-post&utm_campaign=devto)设计*