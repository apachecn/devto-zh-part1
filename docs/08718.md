# 动画组件

> 原文：<https://dev.to/nitin42/animate-components>

[Animate Components](http://animate-components.surge.sh/) 是一个小库，它提供了不同的基本组件，用于在 React 中制作动画。它很灵活，使用非常简单。

## 举例

```
import React, { Component } from 'react';
import { Bounce } from 'animate-components';

class App extends Component {
  render () {
    return (
      <div>
        <Bounce>
          <h1>Hello World</h1>
        </Bounce>
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，您可以将各种道具传递给组件`Bounce`,

1.  **持续时间**
2.  **定时功能**
3.  **延迟**
4.  **方向**
5.  **迭代次数**
6.  **背面可见**
7.  **填充模式**

## 它是如何工作的？

基本上所有的动画组件都被包装在一个高阶函数中，这个函数使用从道具中得到的样式来渲染一个`<span>`元素。它涵盖了构成动画的所有抽象概念，因此您不必担心`@keyframes`规则。只需将属性添加到动画中，就可以了。
对 [GitHub](https://github.com/nitin42/animate-components) 的看法。