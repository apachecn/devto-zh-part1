# 为什么应该使用转换类属性插件

> 原文：<https://dev.to/satansdeer/why-should-you-use-transform-class-properties-plugin-58i>

*原贴于[maksimivanov.com](http://maksimivanov.com/posts/why-you-should-use-transform-class-properties-plugin)T3】*

在我的[上一篇文章](https://dev.to/satansdeer/popup-with-reactjs-for-jekyll-blog-2e7)中，我使用了非常有趣的语法来为我的`Popup`组件定义类方法。我能够使用箭头函数将`this`的范围更改为类级别。嗯，但它实际上不是 Javascript，所以我是怎么做的呢？

首先让我们刷新一下你的记忆，我说的是这段代码:

```
import React, { Component } from 'react';
import Popup from './Popup';
import SubscriptionForm from './SubscriptionForm';

class App extends Component {
  constructor(props) {
    super(props);

    this.state = { isOpen: false };
  }

  openPopup = () => {
    this.setState({
      isOpen: true
    });
  }

  closePopup = () => {
    this.setState({
      isOpen: false
    });
  }

  render() {
    return (
      <div className="App">
        <button onClick={this.openPopup}>
          Click Me!
        </button>

        <Popup show={this.state.isOpen}
          onClose={this.closePopup}>
          <SubscriptionForm></SubscriptionForm>
        </Popup>
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

看，以`openPopup`为例。那正是`transform-class-properties`允许我做的事情。

```
openPopup = () => {
  this.setState({
    isOpen: true
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

它还允许我在这里使用箭头功能。如果不是，那么函数中的`this`将引用全局作用域，而不是`App`类的作用域。可能我会得到一个类似`Uncaught TypeError: Property 'setState' of object [object Object] is not a function`的错误。

## 但是有什么选择呢

更传统和冗长的方法是手动绑定`this`。您可以在`constructor`方法中这样做。

```
 constructor(props) {
    super(props);

    this.openPopup = this.openPopup.bind(this);
    this.closePopup = this.closePopup.bind(this);
    this.state = { isOpen: false };
  } 
```

Enter fullscreen mode Exit fullscreen mode

你必须对每个使用`this`引用的函数都这样做，这是非常重复的。

## 可以在渲染函数中绑定

例如通过使用`bind(this)` :

```
<button onClick={this.openPopup.bind(this)}> 
```

Enter fullscreen mode Exit fullscreen mode

或者使用箭头功能:

```
<button onClick={e => this.openPopup(e)}> 
```

Enter fullscreen mode Exit fullscreen mode

这两种方法都需要额外的麻烦，看起来很难看，并且有性能影响，因为你基本上在每次渲染时都要重新分配函数。

## 总结

这就是为什么你最好使用类级别的属性。顺便说一下，对于未来的 JS 版本，有一个关于职业域的提议，现在已经是 T2 的第三阶段了。这意味着它很有可能成为语言的一部分。

如果您对学习新的 Javascript 特性感兴趣(甚至可能是尚未包括的特性)，请务必订阅我的邮件列表:

[![example button](img/db5f63c2c3319b0c97d16a09029b59ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yRVxmNCa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4jdhz00j3sgqask4xcoz.png) 
*这里这个按钮只是一个图像，去[maksimivanov.com](http://maksimivanov.com/posts/why-you-should-use-transform-class-properties-plugin)看真实的一个*