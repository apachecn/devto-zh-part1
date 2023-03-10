# 高阶什么？

> 原文：<https://dev.to/kayis/higher-order-what>

因此，由于现在是 2017 年，你可能会使用这些基于新*组件*的框架，如 **React** ，你会读到去年所有这些*高阶组件* (HOC)的演讲，但你仍然不明白这是怎么回事。你可能会读到这些*高阶函数* (HOF)，然后问自己，这些讨厌的函数式程序员在做什么。他们是想再次破坏一个完美的面向对象的概念吗？我可以向你保证，他们对你没有恶意...这次！

对于面向对象的人来说，hoc 是继承的替代。它们允许你以动态的方式创作。

```
// Example adding basic HTTP request functions to a component via inheritence

class HttpConnectedComponent extends Component {...}

class UserList extends HttpConnectedComponent {...}

class Application {
  render() {
    return <UserList/>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的问题是显而易见的，你现在有了一个新的基类，如果你想拥有 HTTP 功能，你总是必须扩展它，如果你有一堆其他的*基类*，它会变得很快变得混乱，你的组件现在与基类紧密耦合。

HOCs 允许你回避这个问题。它们是简单的函数，接受一个组件的定义并返回一个组件的新定义。这可能有点...莫名其妙，我知道，但光秃秃的。

让我们先去 HOFs，它们相当简单。它们是将函数作为参数和/或返回函数的函数。

```
 // This HOF takes a function and creates a new one, that will return a 
 // stringified version of the first functions result.

 function stringifyResult(f) {
   return function (arg) { f(arg).toString() }
 }

 function timesTwo(num) { return num * 2 }

 const timesTwoAsString = stringifyResult(timesTwo)

 timesTwo(4) // -> 8
 timesTwoAsString(4) // -> "8" 
```

Enter fullscreen mode Exit fullscreen mode

正如你在`stringifyResult`的函数定义中看到的，你也可以修改传递给`f`的参数，而不仅仅是`f`的结果。所以你可以用这个*模式*对任何函数做各种各样的扩展。

HOCs 现在的想法是，用组件来做这件事。关键是，你最终会得到一个基本组件的增强版本，而不需要扩展其他基类，如果需要，你可以在你的应用程序中的任何地方使用这个新版本。或者，您可以在应用程序中的特定点使用它，根据上下文修改组件的行为，而无需处理组件本身的行为变化(身份验证/授权等)。

例如，您可以编写一个`UserList`组件，它获取一组用户作为道具(在 React 的情况下),然后*用一个`connectHttp` HOC 增强*,这将把`UserList`包装在另一个组件中，该组件将从服务器请求用户数据，并在完成时将其传递给正确的道具。

```
// React Example

// This function creates a new class definition on every call
// It takes an URL, a prop name and a Component class(!) not an object.
function connectHttp(url, prop, Comp) {

  return class extends Component {

    state = {result: null}

    // When it's in the DOM, get the data
    componentDidMount() {
      getData(url).then(result => this.setState({result}))
    }

    // render a placeholder while the data is null
    // render the Component with the data in the right prop, when its not null
    render() {
      const {result} = this.state
      // creating a new props object, that passes the props of "this" component 
      // through AND adds the data of the request
      const props = {...this.props, [prop]: result}

      return result? <Comp {...props}/> : <span>Loading...</span>
    }

  }

}

// Simple user list
// Can be tested stand-alone
// Is completely oblivious to the "source" of the data
function UserList({users=[]}) {
  return (
    <ul>
      {props.users.map(user => <li>{user.name}</li>)}
    </ul>
  )
}

// The wrapped component class
// Can now be used to create lists, that fetch their data themself
const ConnectedUserList = connectHttp('/users', 'users', UserList)

...

<Application>
  <ConnectedUserList/>
</Application> 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，HOC 允许您动态地增强其他组件定义。它创建了一个处理 HTTP 连接的中心点，其他组件可以更简单，从而简化测试和重构。您可以编写一个新的 HOC，它通过 WebSockets 获取数据，您的任何其他组件都不会知道。

这种行为也可以用一个“常规”组件来实现，它只是将数据向下传递给它的子组件，不管它们是什么。不同之处在于，你每次都必须*显式地*包装你的组件，这在更大的应用程序中是相当麻烦的。

像这样包装组件只是使用 HOC 的一种方式，另一种方式是“扩展”你的目标组件。新组件继承自您的组件，而不是您的组件继承自新组件。这使您可以更好地控制组件的行为。例如，您可以覆盖(生命周期)方法。这也简化了组件层次结构，因为只构造了“一个”组件，而不是包装器和目标组件。