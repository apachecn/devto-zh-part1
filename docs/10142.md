# 高阶组件和上下文示例

> 原文：<https://dev.to/kayis/higher-order-components-and-context-example>

在我的前两篇文章中，我写了 React 中的高阶组件和上下文，以及如何使用它们来提高代码的可维护性。在这篇文章中，我将向您展示一个包含这些模式的示例应用程序。

考虑这个简单的列表应用程序:

```
function List(props) {
  return (
    <ul>
      {props.items.map((item, key) =>
        <li key={key}>{item}</li>
      )}
    </ul>
  )
}

function Application() {
  return (
    <div>
      <h1>Application</h1>
      <List items={['Mercury', 'Venus', 'Earth']}/>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

它有一个显示项目的硬编码数据数组。

现在的想法是，用服务器上的项目填充列表。这意味着，在某个地方，我们需要获取数据并将其放入`List`中。

我们可以在`Application`中这样做，但是这个组件可以是需要其他数据的许多其他组件的容器。这不能很好地扩展。

因此，天真的方法是，让数据尽可能靠近需要它的地方，在我们的例子`List`中。

对`List`组件的重写可能是这样的:

```
// This is now a stateful component 
// it handles all its data retrieval
class List extends React.Component {

  // we set an empty array for our items
  // this can be used till the server data arrives
  state = {items: []}

  // after the component is in the DOM we load the server data
  // the URL is in the prop "src"
  // we reformat it a bit and store it in the components state
  componentDidMount() {
    fetch(this.props.src)
    .then(r => r.json())
    .then(tasks => this.setState({
      items: tasks.map(t => t.title),
    }))
  }

  // for every render we just map over the items
  // and create an <li> for every one (0 in the first run)
  render() {
    return (
      <ul>
        {this.state.items.map((item, key) =>
          <li key={key}>{item}</li>
        )}
      </ul>
    )
  }

}

// the application doesn't need to supply items here
// instead a source URL is needed
function Application() {
  return (
    <div>
      <h1>Application</h1>
      <List src='http://jsonplaceholder.typicode.com/todos'/>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

这对于简单的组件来说非常有效，它将数据检索从容器对象中分离出来，并使`List`的使用更加灵活。它甚至可以在不同的地方使用不同的源 URL。

但是现在,`List`的可视部分与数据检索紧密耦合，这使得测试创建的 DOM 元素是否正确变得更加困难。您总是需要一个服务器或者至少是一个模拟服务器来获取正确的数据。

此外，您可能希望以后在同一个`List`组件中呈现不同的服务器响应。

解决这个问题的一个办法是将 hoc 与上下文结合起来，就像前两篇文章中提到的那样。

首先创建一个*服务提供者组件*，它处理数据检索并将结果注入到上下文中。

其次，您创建一个更高阶的组件,它将从上下文中收集正确的数据，并将其注入到其子组件的 props 中。此外，它还会触发检索。

让我们来看第一个任务，服务提供商:

```
class ItemProvider extends React.Component {

  // in this simple example all items get written in the same place
  // in a more complex system, you could make this configurable too.
  state = {items: []}

  // this method gets data from a source URL
  getItems(src) {
    fetch(src)
    .then(r => r.json())
    .then(items => this.setState({items}))
  }

  // this method provides components, who are potentially deep in the app tree
  // with two things, a list of items and a function to load these items
  // here we use the method getItems to load the data
  getChildContext() {
    return {
      items: this.state.items,
      getItems: this.getItems.bind(this),
    }
  }

  render() {return this.props.children}

}

ItemProvider.childContextTypes = {
  items: React.PropTypes.array,
  getItems: React.PropTypes.func,
} 
```

Enter fullscreen mode Exit fullscreen mode

第二个任务是高阶组件:

```
// The first function takes configuration
// in which prop the items should be inserted
// where the items should be loaded from
// and a function that formats each item to the
// expected format of the wrapped component
// this returns a second function that takes a component to wrap
const connectItems = (targetProp, src, format) => Comp =>  {

  // if the component that should be wrapped is supplied, we create a HOC
  class ItemComponent extends React.Component {

    // when its mounted to the DOM, it will use the getItems function
    // supplied by the provider somewhere at the top of the component tree
    // and tell it where the data should be loaded from
    componentDidMount() {
      this.context.getItems(src)
    }

    // when the component renders it simply renders the wrapped component
    render() {
      // the props of the HOC will be passed down to the wrapped component
      // this allows to apply styling and such
      // and the items from the provider will be formatted
      // and stored in the target prop of the wrapped component
      const newProps = {
        ...this.props,
        [targetProp]: this.context.items.map(format),
      }
      return <Comp {...newProps}/>
    }
  }

  // the HOC needs to tell React, that it needs 2 context variables
  // the getItems function to start the data retrieval
  // the items array to be passed down to the wrapped component
  ItemComponent.contextTypes = {
    items: React.PropTypes.array,
    getItems: React.PropTypes.func,
  }

  return ItemComponent

} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有一个提供者 wo 负责数据检索，还有一个特设机构告诉提供者*何时*加载数据。它还将它传递给它的包装组件，进入正确的道具。

最后，我们只需要把所有的东西整合在一起:

```
// List component can stay as it is
// so it's easier to test and easier to reuse
function List(props) {
  return (
    <ul>
      {props.items.map((item, key) =>
       <li key={key}>{item}</li>
      )}
    </ul>
  )
}

// Here we wrap the List with our HOC
// first argument is the target prop of List for the items
// second argument is the target URL the items should be fetched from
// third argument is the format function that tells
// which part of an item should be used as text
// at last we add the component we want to wrap.
const ConnectedList = connectItems(
  'items',
  'http://jsonplaceholder.typicode.com/todos',
  item => item.title
)(List)

class Application extends React.Component {

  // First we wrap the whole Application with our provider
  // then, somewhere deeper in the element tree, we add the Connected list
  render() {
    return (
      <ItemProvider>
        <div>
          <h1>Application</h1>
          <ConnectedList/>
        </div>
      </ItemProvider>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果我们的服务改变了，我们只需要改变`ItemProvider`，如果它保持它的 API ( `context.getItems(src)`和`context.items`)，它对应用程序的其余部分是完全透明的。