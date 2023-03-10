# 使用 React 的 Redux API 中间件

> 原文：<https://dev.to/committedsw/redux-api-middleware-with-react-13pl>

当使用 Redux 编写 React 应用程序时，您无疑需要调用某种 web 服务来为组件提供信息。

但是这些 api 调用应该放在哪里呢？

## 容器内部组件

最明显的地方是组件本身，将 api 调用放在组件层次结构中较高的[容器组件](http://redux.js.org/docs/basics/UsageWithReact.html)中，作为道具传递给表示组件:

```
class UserPage extends Component {

  componentWillMount(){
    this.updateUser(this.props.userId)
  }

  componentWillReceiveProps(newProps){
    if(newProps.userId !== this.props.userId){
      this.updateUser(newProps.userId)
    }
  }

  updateUser(userId) {
    request.get('/api/user/' + userId)
      .end( function(err, res) {
        this.setState({user: res.body})
      })
  }

  render(){
    return (
      <UserInfo user={this.state.user} />
    )
  }

  ...
} 
```

然而，这意味着容器组件需要管理状态:来自 api 调用的响应，以及发生的任何错误。

您可能还想尽可能地异步调用 api。上面的例子将阻塞在`componentWillMount()`上，延迟组件的初始呈现，所以我们希望将调用包装在承诺或生成器中。我们可能还想在请求过程中存储状态，以便组件可以设置一个微调器。容器至少有三种不同的“模式”:获取、收到成功响应或收到错误。在一个组件中有一个以上的 API 调用会使这个数至少增加 3 倍。这些组件变得更难推理，并且更容易出错。

最重要的是，单元测试组件变得更加困难，因为需要模拟 api 调用。与 redux 相比，商店的一部分是通过道具接收的。在组件需要什么数据和如何请求数据之间没有抽象。我们需要分离我们的关注点。

## 内减速器

我们希望尽可能避免组件中的状态，使用 Redux 是实现这一点的好方法。如果我们可以从 redux 内部进行 API 调用，那么组件可以简单地从 Redux store 订阅 API 更新。不需要难以推理的大的有状态组件。

但是这感觉不太对:reducers 只应该作用于动作的内容，而不是任何繁重的工作。不仅如此，减速器是同步的。我们不希望 API 调用阻塞整个应用程序的所有状态更改！

让我们回忆一下[异步操作(冗余文档)](http://redux.js.org/docs/advanced/AsyncActions.html):

> 当您调用异步 API 时，有两个关键时刻:开始调用的时刻和收到应答(或超时)的时刻。
> 
> 这两个时刻通常都需要改变应用程序的状态；要做到这一点，您需要调度将由 reducers 同步处理的普通操作。通常，对于任何 API 请求，您都希望调度至少三种不同的操作:
> 
> 通知缩减者请求已开始的操作。
> 
> 通知缩减者请求已成功完成的操作。
> 
> 通知缩减者请求失败的操作。

因此，我们希望从我们的容器组件中分派一个动作，该组件在发送请求时分派另一个动作。然后，当来自服务器的响应返回时，根据请求是否成功，分派一个成功操作或错误操作。

我们需要为每个 API 端点重复这种模式…

## 中间件来救援了

redux 中间件让我们在 redux 动作通过 Redux 管道到达 Redux 之前拦截它们，这使得它们非常适合处理横切关注点。

优秀的 [redux-api-middleware](https://www.npmjs.com/package/redux-api-middleware) 库允许我们声明性地指定动作，以及前面提到的三个 api 动作，以便中间件代表我们进行调度。

```
import { CALL_API } from 'redux-api-middleware'

export const USER_REQUEST = 'user/REQUEST'
export const USER_SET = 'user/SET'
export const USER_ERROR = 'user/ERROR'

export function updateUser(userId) {
  return {
    [CALL_API]: {
      endpoint: '/api/user/' + userId,
      method: 'POST',
      types: [USER_REQUEST, USER_SET, USER_ERROR]
    }
  }
} 
```

然后，当 api 中间件发出动作时，我们的 reducer 可以更新存储:

```
import { handleActions } from 'redux-actions'

const user = handleActions({

  [USER_REQUEST]: (state, action) => ({
    isFetching: true,
    isError: false,
    user: state.user
  }),

  [USER_SET]: (state, action) => ({
    isFetching: false,
    isError: false,
    user: action.payload // the API response body
  }),

  [USER_ERROR]: (state, action) => ({
    isFetching: false,
    isError: true,
    user: null
  })

})

export default user 
```

## 总结

生态系统正在以惊人的速度发展:不缺乏其他解决方案:ES6 生成器、新的 Fetch API 和库，如 [react-refetch](https://github.com/heroku/react-refetch) 。

但是如果您想知道将 api 请求放在支持 Redux 的应用程序中的什么地方，那么中间件是一个不错的选择。

鸣谢:丹尼尔·汉森在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片