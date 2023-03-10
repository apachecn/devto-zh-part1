# 了解 React 组件中的状态

> 原文：<https://dev.to/sarah_chima/understanding-state-in-react-components-1f1>

像 [React props](https://dev.to/sarah_chima/react-properties-cml) 这样的状态用于在 React 中存储数据。`state`和`props`的区别在于，`props`存储的数据在其生命周期内是固定的，而`state`存储的数据可以随着时间的推移而改变。这种改变可以是对用户动作、网络响应或任何事情的响应。另外，当`props`由父组件设置时，`state`由组件自身在内部改变。

组件的状态应该被视为私有数据。该数据不向使用它的组件公开。它是私有的，完全由组件控制。它只出现在组件定义的内部。您可以将`state`视为影响组件渲染的内部数据集。
有状态的组件称为有状态组件，没有状态的组件称为无状态组件。

您可以设置组件的默认状态，访问当前状态并更新该状态。让我们考虑一下如何做到这一点。我们将引用我在关于 [React 组件](https://dev.to/sarah_chima/an-introduction-to-react-components-cke)的文章中创建的`<Greeting />`组件。
这是创建的组件。

```
class Greeting extends React.Component {
  render() {
    return (
      <div className = "box">
        <h2> Hello Human Friend!!!</h2>
        <p> We are so glad to have you here. </p>
      </div>
    );
  }
}

ReactDOM.render(<Greeting />, document.getElementById("app")); 
```

Enter fullscreen mode Exit fullscreen mode

### 设置组件的初始状态

```
class Greeting extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name : "Human Friend", 
      message : "You are welcome to our World"
    }
  }... 
```

Enter fullscreen mode Exit fullscreen mode

以上是设置组件默认状态的语法。`constructor`方法是一种特殊的方法，用于创建和初始化用类创建的对象。在任何类中，只能有一个`constructor`方法。`super`允许你调用父类的构造函数方法。你需要在你的构造函数中包含`props`来访问`this.props`。在 React 中，`super`应该出现在一个`constructor`方法中。

您可以使用`this.state`来设置组件的初始状态。`this.state`应该等于一个对象，就像上面的例子一样。所以在我们的例子中，组件的初始状态被设置为

```
this.state = {
      name : "Human Friend", 
      message : "You are welcome to our World"
    } 
```

Enter fullscreen mode Exit fullscreen mode

组件的初始状态就是这样设置的。那么我们如何访问组件的状态呢？

### 访问组件的状态

为了访问一个组件的状态，我们使用`this.state.name-of-property`。例如，如果我们想访问`name`的状态，我们只需使用`this.state.name`。
现在让我们改变组件的初始状态。

```
render() {
    return (
      <div className = "box">
        <h2> Hello {this.state.name}!!!</h2>
        <p> {this.state.message}</p>
      </div>
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

如果你理解了这一点，你就做得很好。接下来，让我们看看如何改变组件的状态。

### 更改组件的状态

为了改变组件的状态，我们调用一个函数`this.setState()`。`this.setState()`接受两个参数，一个对象和一个回调。因此，在我们的问候组件中，如果我们要调用 this . setstate({ name:" Sarah " }),`name`属性的状态将被更改为“Sarah”。酷！！！

所以回到我们的`<Greeting />`组件。让我们看看`this.setState`的行动。我们要做的是创建一个按钮，在初始状态`this.state.name`和新的`this.state.name`之间切换。

首先我们添加按钮。

```
render() {
    return (
      <div className = "box">
        <h2> Hello {this.state.name}!!!</h2>
        <p> {this.state.message}</p>
        <button onClick = {this.handleClick}> Click Me </button>
      </div>
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们添加一个处理点击的方法。

```
handleClick() {
   const newName = this.state.name == "Sarah" ? "Human Friend" : "Sarah";
   this.setState({name : newName});
}
  ... 
```

Enter fullscreen mode Exit fullscreen mode

所以上面的代码做的就是检查`this.state.name`的值是否为“Sarah”。如果是，`newName`的值为“人类朋友”，反之亦然。然后我们将`this.state.name`设置为`newName`的值。

最后一点，在 JavaScript 中，类方法默认是不绑定的。如果你忘记绑定`this.handleClick`并将其传递给`onClick`，那么在实际调用函数时`this`将会是未定义的。所以我们必须将我们的方法绑定到构造函数方法中的`this`。

```
class Greeting extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name : "Human Friend", 
      message : "You are welcome to our World"
    }
    this.handleClick = this.handleClick.bind(this);
  } 
```

Enter fullscreen mode Exit fullscreen mode

很高兴你读到这里。这是我们为`<Greeting/>`组件编写的全部代码。

```
class Greeting extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name : "Human Friend", 
      message : "You are welcome to our World"
    }
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    const newName = this.state.name == "Sarah" ? "Human Friend" : "Sarah";
    this.setState({name : newName});
  }

  render() {
    return (
      <div className = "box">
        <h2> Hello {this.state.name}</h2>
        <p> {this.state.message} </p>
        <button onClick = {this.handleClick}>Click Me </button>
      </div>
    );
  }
}

ReactDOM.render(<Greeting />, document.getElementById("app")); 
```

Enter fullscreen mode Exit fullscreen mode

要了解这是如何工作的，[在 Codepen](https://codepen.io/sayrah901/pen/EwJNjQ) 上试试。

因此，我们已经能够理解状态，并学习如何初始化、访问和更新组件的状态。厉害！！！

有什么问题吗？有什么补充吗？欢迎发表评论。

感谢您的阅读:)