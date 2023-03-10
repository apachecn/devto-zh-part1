# 重新思考 Redux(并减少其样板文件)

> 原文：<https://dev.to/emilios1995/rethinking-redux-and-reducing-its-boilerplate>

*TL；dr: [还原创建模块](https://github.com/Emilios1995/redux-create-module)T3】*

假设您想在 react-redux 应用程序中创建一个新模块。你知道该怎么做。
创建动作类型常量，make 和动作创建器，处理 reducer 中的类型。
你可能已经注意到，这个过程看起来几乎每次都是一样的*。作为开发人员，我们知道这类事情可以而且应该被抽象和自动化。*

 *所以，首先让我们想一想什么是开发者体验的最佳解决方案。或者，既然*我们是*开发者，让我们重新考虑 Redux 吧。

嗯，如果我们只对将动作映射到状态感兴趣，我首先想到的是制作一个 T2 映射。或者一个普通的物体。

```
const counterMap = {
  increment: (state) => state + 1,
  decrement: (state) => state -1
}
// How do we use it? Simple. With a reducer.
const reducer = (state = 0, action) => 
  (counterMap[action.type]) 
    ? counterMap[action.type](state) 
    : state 
```

Enter fullscreen mode Exit fullscreen mode

但是我们不想创造减速器。我们只想考虑地图。让我们创建一个函数，它获取一个地图并返回一个归约器

```
const createReducer = (initialState, map) =>
   (state = initialState, action) => 
     (map[action.type]) 
       ? map[action.type](state) 
       : state

const reducer = createReducer(0, counterMap)

reducer(0, {type: "increment"}) // => 1 
```

Enter fullscreen mode Exit fullscreen mode

这么简单！或者说，太简单了！因为我们这里有个问题。动作创建器的发明是有原因的，那是因为有些动作需要有一个有效载荷来处理...或者是？
不！当然，还有另一个原因，那就是将字符串传递给 reducer 是不安全和无效的。如果我们打错字了呢？！
那么*现在让我们认真起来。*

我们仍然不想手动创建动作创建者，而且，我们为什么要这样做呢？
想想看，`createReducer`已经拥有了制造它们所需的所有信息。
它可以从地图的`keys`中获取我们的动作类型。
所以，让它既返回一个 reducer，又返回动作创建者，命名为`createModule`

```
const createModule = (initialState, map) => {
  const reducer = (state = initialState, action) => 
     (map[action.type]) 
       ? map[action.type](state) 
       : state
  const actions = Object.keys(map).reduce(
    (acc, type) =>
      Object.assign(acc, {
        [type]: (payload = {}) => ({ type, payload })
      }),
    {}
  );
}  
// example
const counterMap = {
  add: (state, {payload}) => state + payload
}
export const counter = createModule(0, counterMap)

// then somewhere in a connected component...
dispatch(counter.actions.add(2)) 
```

Enter fullscreen mode Exit fullscreen mode

整洁！

当然，我们还有一些 todos。比如命名我们的动作以避免冲突，允许缩减器处理来自另一个模块的动作。但是我们不会在这篇文章中讨论这个问题。

相反，我会向您介绍我制作的[小模块](https://github.com/Emilios1995/redux-create-module)的源代码。

感谢阅读！

*照片:[海上日出【我的 T2】:](https://500px.com/photo/157723175/sunrise-in-the-sea-by-emilio-srougo)**