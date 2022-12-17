# 反应属性

> 原文：<https://dev.to/sarah_chima/react-properties-cml>

上次我在[上写了 React 组件](https://dev.to/sarah_chima/an-introduction-to-react-components-cke)。这一次，我们将讨论每个组件都有的东西——属性。我将利用我们在那篇文章中创建的组件来解释属性。因此，如果你对[反应组件](https://dev.to/sarah_chima/an-introduction-to-react-components-cke)不太熟悉，你可能会想读一读。

组件`props`是保存关于该组件的信息的对象。它们类似于 HTML 中的属性。事实上，它们被传递给组件，就像属性被传递给 HTML 一样。

这是我们在上一篇文章中制作的组件的代码。

```
import React from "react";
import ReactDOM from "react-dom";

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

### 向组件添加属性

因此，假设我们想要向我们创建的`Greeting`组件的 name 属性传递一个值。我们会做到这一点。

```
<Greeting name = "Sarah" /> 
```

很简单，对吧？一个组件可以有任意多的属性。

```
<Greeting name = "Sarah" message = "I will like you to be my friend" blah = "blah blah blah" /> 
```

记下传递属性的必要条件。你需要一个名字，在上面的例子中是`name` `message`和`blah`。属性的名称可以是您选择的任何名称。第二件事是您希望该属性存储的信息。在上面的例子中，这些是“莎拉”、“我希望你成为我的朋友”和“等等等等”。如果我们想传递不是字符串的信息，比如一个函数、一个对象、一个数组或一个变量，它必须用花括号括起来。

```
<Greeting message = {greetings} myInfo = /> 
```

我们如何访问和显示刚刚传递给组件的属性呢？

### 访问属性

我们使用`this.props`来访问传递给组件的属性。`this.props`对象包含一个组件拥有的所有属性。让我们回到我们的`Greeting`组件的例子。假设我们希望用户能够传递要打招呼的人的名字和要传递的消息。我们将这样做。

```
class Greeting extends React.Component {
  render() {
    return (
      <div className = "box">
        <h2> Hello {this.props.name}!!!</h2>
        <p> {this.props.message} </p>
      </div>
    );
  }
} 
```

我们刚刚做的是告诉组件呈现传递给 name 属性和 message 属性的任何信息。所以如果我们这样做

```
ReactDOM.render(<Greeting name = "Sarah" message = "I want to be your friend" /> , document.getElementById("app")); 
```

这就是我们正在渲染的

```
 ...
      <div className = "box">
        <h2>Hello Sarah!!!</h2>
        <p>I want to be your friend</p>
      </div>
      ... 
```

### 处理事件

有时候我们想把函数传递给一个属性。例如，你想要一个函数来处理像`onClick`或`onKeyDown`这样的事件，如何实现呢？
让我们用我们的例子来解释这一点。让我们在问候语中添加一个按钮，单击该按钮时会显示“hello”。

首先，我们将为处理`onClick`事件的`Greeting`组件添加一个方法。这个方法将在`render()`之外。

```
class Greeting extends React.Component {

    handleClick () {
        alert ("Hello Human");
    }

    render() { ... 
```

然后我们将该方法传递给我们添加的按钮的`onClick`属性。

```
...
   render () {
      return (
          <div className = "box">
            <h2>Hello {this.props.name}!</h2>
            <p>{this.props.message}</p>
            <button onClick = {this.handleClick}>Click Me</button>
          </div>
         );

    } 
```

### 默认属性

您还可以将默认属性传递给组件。例如，如果一个人使用了`Greeting`组件而没有传递任何属性给它，那该怎么办？我们不想让新认识的人认为我们不友好。在这种情况下，我们可以为组件设置默认的`props`。为此，我们为组件添加了一个默认值属性。这个`defaultProps`一定等于一个物体。

```
 Greetings.defaultProps = {name : "Human Friend", message : "You are welcome to our world"}; 
```

这将被添加到类声明之后。

### 接触孩子们

组件也可以有子组件。例如，`Greeting`组件可以写成

```
 <Greeting>Hello</Greeting> 
```

这仍然有效。为了访问这个组件的子组件，在本例中是字符串`Hello`，我们使用`this.props.children`。有了这个我们可以访问两个标签之间的任何东西。

**注意**最后要注意的是属性是只读的。组件不应该修改它自己的`props`。`props`存储只能被另一个组件修改而不能被自身修改的信息。

这里有一个[代码笔](https://codepen.io/sayrah901/pen/XeGOwo)，展示了我们到目前为止所做的一切。试着玩它，直到你确定你明白我们做了什么。

有什么问题吗？有什么补充吗？欢迎发表评论。

感谢您的阅读:)