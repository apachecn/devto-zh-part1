# React 组件简介

> 原文：<https://dev.to/sarah_chima/an-introduction-to-react-components-cke>

任何用户界面都可以分成更小的部分。这些部分是 UI 的组件。例如，您现在正在查看的页面(开发人员页面)。让我们试着把它分成更小的部分。首先，我们有两个主要部分:页面顶部的导航栏和包含您正在阅读的文本的正文。导航条可以进一步分解成更小的部分。我们有一个搜索字段，一个`write a post`的链接，一个通知图标和一个顶部有图像的下拉菜单。这些部分中的每一个都是组成这个页面导航栏的组件。它们可以被称为导航栏的子组件。所以页面是由包含子组件的组件(导航条和正文)组成的。UI 越复杂，可以分解成的组件就越多。现在就来一个合适的定义吧。

组件是可重用的代码块。组件使得将任何 UI 分成独立的、可重用的部分并孤立地考虑这些部分成为可能。就像一个纯粹的函数一样，一个组件应该只做一件事。我们还在等什么？让我们马上创建一个组件。

我们将创建一个欢迎人们访问页面的组件。这真的很基本，但重要的是我们从这开始，这样我们就可以很容易地理解如何创建和渲染一个组件。这是我们想要实现的目标的代码。

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

Enter fullscreen mode Exit fullscreen mode

如果你不理解代码中的内容，那也没关系。我将简要解释每一部分。让我们从第一部分开始。

```
import React from "react";
import ReactDOM from "react-dom"; 
```

Enter fullscreen mode Exit fullscreen mode

`react`和`react-dom`是 JavaScript 库。`react`是 React 库。它包含使用 React 所需的方法。`react-dom`是一个 JavaScript 库，包含几个以某种方式处理 DOM 的方法。我们在这里做的只是将这些库赋给变量，这样它们就可以在 js 文件中的任何地方使用它们的方法。

```
class Greeting extends React.Component {
  ... 
```

Enter fullscreen mode Exit fullscreen mode

以上是创建组件类的语法。这里我们利用 [ES6 类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)来创建一个组件类。这个组件类本身不是一个组件，而是一个用来创建组件的工厂。困惑吧。它类似于一个 CSS 类。在 CSS 文件中定义一个类。然后你可以在你的 HTML 文件中的几个地方使用这个类。每次使用类时，该类的所有属性都会添加到使用它的 HTML 元素中。因此，在这里，您定义了一个组件类，并使用它来创建组件，其方法与组件类中定义的方法相同。

`Greeting`是您的组件类的名称。在 React 中，组件通常以大写字母开头，以区别于普通的 HTML 元素。`extends`关键字用在类声明或类表达式中，创建一个类作为另一个类的子类。也就是说，创建一个类的子类。

要创建这个组件类，您需要使用 React 库中的基类`React.Component`。当你这样做的时候，你实际上是在子类化`React.Component`。所以以上是创建组件类的语法。

**注意**:这不是创建组件的唯一方法。我只是觉得这个更方便。

```
render() {
    return (
      <div className = "box">
        <h2> Hello Human friend</h2>
        <p> We are so glad to have you here. </p>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`render()`是每个组件类的重要组成部分。它应该出现在每个组件类中。它还必须包含 return 语句。基本上，`render()`是一个定义组件中应该返回什么的函数。这可能是一个 react 元素、一个字符串或数字，甚至是一个组件。
render()函数应该是纯的。这意味着它不修改组件状态，每次调用它都返回相同的结果，并且它不直接与浏览器交互。
所以在我们的组件中，`render()`函数将返回一个元素，即`<div>`。

最后，

```
ReactDOM.render(<Greeting />, document.getElementById("app")); 
```

Enter fullscreen mode Exit fullscreen mode

`ReactDOM.render`使您的组件出现在屏幕上。请记住，在 JSX 中，右尖括号前必须始终包含正斜杠。

同样，这里有一个到 [Codepen](https://codepen.io/sayrah901/pen/pWGQjq) 的链接，在这里你可以看到刚刚创建的组件。你可以一直玩它，以确保你理解它。

现在，如果我想在应用程序的任何其他部分欢迎人们，我只需使用`<Greeting />`组件。酷！！！

这就是 React 组件的介绍。有什么问题吗？有什么补充吗？欢迎发表评论。

感谢您的阅读:)