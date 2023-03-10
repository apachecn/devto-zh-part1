# 用 Webpack 2 延迟加载 React 组件

> 原文：<https://dev.to/kayis/lazy-loaded-react-components-with-webpack-2>

*TL；DR 这里是一个[库](https://github.com/kay-is/webpack-react-hoc)和一个工作实例。:)*

在我过去几周的帖子中，我告诉了你两件事。[如何用高阶组件(HOC)改进你的 React 代码库](https://dev.to/kayis/higher-order-with-context)和[如何用 Webpack 2 让你的单页应用的部分按需加载](https://dev.to/kayis/dynamic-imports-with-webpack-2)。

在这篇文章中，我将向你展示如何把这些合并成一个。

为什么？

因为很酷！

它让你节省更多的带宽。

你可以创建模态、弹出窗口、上下文菜单、通知、对话框等等，当需要这些特性时，所需的代码就会被加载。

我不是说这是银弹，你应该和你的用户一起检查哪些东西不是经常需要或者太重，并且只把它们移到其他块中，否则你的用户将会为他们的每次点击加载指示器。

不管怎样，让我们开始吧！

我将引用我制作的示例[库](https://github.com/kay-is/webpack-react-hoc)中的代码。

首先我创建了一个`<BigList>`组件，它基本上是一个大约有 1000 个列表项的未排序列表，这将模拟一点加载时间。

其次，我创建了一个名为`lazify`的 HOC，用于为我们的`<BigList>`创建一个包装器。

Lazify 不同于常规 hoc，因为它不能获得对它所包装的组件的构造函数的引用。因为，呃，它现在还没上膛。

我们也不能简单地给它带有目标组件定义的文件路径。

为什么不呢？

因为 Webpack 在代码中搜索`System.import(...)`并根据 path 参数创建新的块。如果它发现类似于`System.import(variable)`的东西，它就不能对“哪些”文件应该移动到另一个块做出任何有根据的猜测。

好的，那么我们必须给 lazify `System.import('/path/to/our/Component')`？

不，因为这将在调用`lazify`函数时执行，并且`lazify`将得到承诺，该承诺解析到模块。

那么，我们需要传递什么给`lazify`来让事情运行，但前提是我们组件的懒惰版本被渲染了？

嗯，你在任何地方都可以使用的最好的东西，*一个函数*。

需要时会调用`System.import`的函数。

因此，不要像这样创建延迟加载版本:

```
import BigList from 'BigList'
import lazify 'lazify'

export default lazify(BigList) 
```

Enter fullscreen mode Exit fullscreen mode

这将静态加载所有内容，不会给我们带来任何好处。

我们的文件应该是这样的:

```
import lazify from 'lazify'

export default lazify(() => System.import('BigList')) 
```

Enter fullscreen mode Exit fullscreen mode

现在`lazify`能够创建我们的包装组件(WC)并让它访问加载正确导入的函数。

```
import React from 'react'

export default
function lazify(load) {

  return class LazyComponent extends React.Component {

    constructor() {
      super()
      this.state = {Component: null}
    }

    componentDidMount() {

      load().then(m => this.setState({Component: m['default']}))

    }

    render() {

      const {props} = this
      const {Component} = this.state

      return Component? <Component {...props}/> : <span>Loading...</span>

    }

  }

} 
```

Enter fullscreen mode Exit fullscreen mode

新组件将具有初始化为`null`的`Component`状态。

第一次挂载时，它会给出一个加载指示，然后 React 会调用`componentDidMount`，我们的 WC 会调用之前传递给`lazify`的`load`函数。

这个函数调用`System.import`并将返回它的承诺。承诺将解析到我们加载的模块，这应该导出一个默认的 React 组件。

默认导出时，我们真正想要渲染的*真实组件* (RC)存储在 WC 的状态中，WC 将再次渲染，这一次使用 RC 并将其`props`传递给它，因此 WC 的父级不会知道发生了什么。

我对这里的目录结构的想法是将 RC 存储在一个`index.js`中它自己的文件夹中，将 WC 存储在`lazy.js`中。

所以当你使用它的时候，你会意识到它是一个懒惰的版本。

```
import BigList from 'BigList'
import LazyList from 'BigList/lazy' 
```

Enter fullscreen mode Exit fullscreen mode