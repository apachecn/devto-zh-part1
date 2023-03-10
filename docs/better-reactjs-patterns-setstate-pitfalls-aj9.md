# 更好的反应模式:this.setState 陷阱

> 原文：<https://dev.to/promisetochi/better-reactjs-patterns-setstate-pitfalls-aj9>

将对象文字传递给 setState 存在一个潜在的问题，如下所示，

```
this.setState({someKey: someValue}) 
```

Enter fullscreen mode Exit fullscreen mode

下面的代码片段说明了潜在的问题。我们连续三次快速调用 setState，并在每次调用后添加一个回调来将更新后的状态记录到控制台。

```
 state = {
  counter: 0
}

incrementCounter = () => {
  this.setState(
    {
      counter: this.state.counter + 1
    },
    () => console.log()
  )
}

componentDidMount = () => {
  incrementCounter()
  incrementCounter()
  incrementCounter()
}

//output

{counter: 1}
{counter: 1}
{counter: 1} 
```

Enter fullscreen mode Exit fullscreen mode

您可能期望输出是:

```
{counter: 1}
{counter: 2}
{counter: 3} 
```

Enter fullscreen mode Exit fullscreen mode

导致意外输出的原因有两个:

1.  异步更新
2.  批量更新

下面的代码片段可以很好地描述 Reacts 异步更新:

```
state = {
  counter: 0
}

incrementCounter = () => {

  this.setState(
    {
      counter: this.state.counter + 1
    }
  )

  console.log(this.state.counter) //this will always return the state before the new state above is reflected in state
}

incrementCounter() // 0
incrementCounter() // 1 
```

Enter fullscreen mode Exit fullscreen mode

对 incrementCounter 的第一次调用记录的不是 1，而是 0，第二次调用记录的是 1，而不是 2，这样继续下去。

下面的代码示例在官方文档中描述了批量更新，

```
Object.assign(
  previousState,
  {quantity: state.quantity + 1},
  {quantity: state.quantity + 1},
  ...
) 
```

Enter fullscreen mode Exit fullscreen mode

所以我们最初的代码片段实际上被转换成这样，

```
Object.assign(
  previousState,
  {counter: state.counter + 1},
  {counter: state.counter + 1},
  {counter: state.counter + 1}) 
```

Enter fullscreen mode Exit fullscreen mode

那么，如何通过向 setState 传递函数而不是对象来避免这些潜在的问题呢？

```
incrementCounter = () => {
  this.setState((presentState) => (
    Object.assign({}, presentState, {
      counter: presentState.counter + 1
    })
  ))
}

componentDidMount = () => {
  incrementCounter()
  incrementCounter()
  incrementCounter()
}

//output

{counter: 3}
{counter: 3}
{counter: 3} 
```

Enter fullscreen mode Exit fullscreen mode

这样，setState 方法将始终向函数传递最新状态。注意，我们使用 Object.assign 从当前状态创建一个新对象。

注意，你不应该这样做，

```
this.setState((presentState) => {
  presentState.counter+= 1
  return presentState
}) 
```

Enter fullscreen mode Exit fullscreen mode

虽然上面会导致状态更新和重新渲染，但下面的代码片段不会，因为 React 的比较比较肤浅。

```
state = {
  someProp: {
    counter: 0
  }
}
this.setState((presentState) => {
  presentState.someProp.current += 1
  return presentState
}) 
```

Enter fullscreen mode Exit fullscreen mode

当新状态不依赖于旧状态时，向 setState 传递一个对象文本仍然是安全的，但是向它传递函数是一种更好的模式。如果你熟悉 Redux，它类似于 Redux 的减速器。

您可能已经注意到我在 incrementCounter 方法中使用了箭头函数。这是提议的 es7 [属性初始化语法](https://github.com/tc39/proposal-class-public-fields)，你现在可以用巴别塔[转换类属性插件](https://babeljs.io/docs/plugins/transform-class-properties/)来使用它。

干杯。