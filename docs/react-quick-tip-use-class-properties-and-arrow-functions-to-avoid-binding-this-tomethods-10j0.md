# 快速反应提示:使用类属性和箭头函数来避免将“this”绑定到方法上

> 原文：<https://dev.to/davejs/react-quick-tip-use-class-properties-and-arrow-functions-to-avoid-binding-this-tomethods-10j0>

当您想要访问 React 组件的类方法内部的`this`时，您需要将它绑定到您的方法:

```
class Button extends Component {
  constructor(props) {
    super(props);
    this.state = { clicked: false };
    this.handleClick = this.handleClick.bind(this);
  }
  handleClick() {
    this.props.setState({ clicked: true });
  }
  render() {
    return <button onClick={this.handleClick}>Click Me!</button>;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

将这个绑定到构造函数中的`handleClick`允许我们在`handleClick`中使用来自`Component`的 this.setState。如果没有这个绑定，`this`将被重新定义为`handleClick`的作用域，并丢失组件的`setState`方法的上下文。

但这完全没有必要，多余的代码！

你可以通过使用一些新的 ES6+功能来清理这种丑陋。下面是相同的组件，使用类属性和箭头函数重写，以避免将`this`绑定到`handleClick` :

```
class Button extends Component {
  state = { clicked: false };
  handleClick = () => this.setState({ clicked: true });
  render() {
    return <button onClick={this.handleClick}>Click Me!</button>;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意:为了使用类属性，你必须在你自己的 Babel 配置中启用 transform-class-properties。如果您正在使用 Create React App，这已经为您启用了。

## 这是为什么？

这是因为两个原因:

1.  箭头函数本质上不会重定`this`的作用域，所以我们不需要在类构造函数中绑定`this`。
2.  JavaScript 有一级函数，这意味着函数和数据被同等对待。因此，箭头函数可以分配给变量，或者在本例中，分配给类属性。

* * *

## 奖励提示

注意，在第二个例子中，我也将 state 定义为一个类属性，这样就不需要构造函数了。