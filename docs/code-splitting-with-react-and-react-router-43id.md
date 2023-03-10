# 使用 React 和 React 路由器 V4 进行代码拆分

> 原文：<https://dev.to/tylermcginnis/code-splitting-with-react-and-react-router-43id>

代码分割最近越来越受欢迎，因为它允许你将你的应用程序分割成独立的包，用户可以逐步加载。在这篇文章中，我们不仅要了解什么是代码拆分以及如何实现，还要了解如何用 React 路由器实现它。

### 视频

[https://www.youtube.com/embed/bUlkq3PDfRY](https://www.youtube.com/embed/bUlkq3PDfRY)

### 岗位

现在是 2018 年。你的用户不应该下载你的整个应用程序，当他们需要的只是其中的一部分。如果用户正在创建一个新的帖子，让他们下载注册视图的所有代码是没有意义的。如果用户正在注册，他们不需要你的应用程序在设置视图上需要的巨大的富文本编辑器。这是一种浪费，有些人会认为这是对那些没有无限带宽特权的用户的不尊重。这个想法不仅在最近几年越来越受欢迎，而且也变得更加容易实现——它甚至有一个很酷的名字——代码分割。

想法很简单，不要下载代码，直到用户需要它。在实践中，它可以更复杂一点。这样做的原因不是因为代码拆分本身非常困难，而是因为有各种各样的工具可以做到这一点，并且每个人都有一个关于哪个是最好的观点。当你第一次开始时，可能很难分析什么是什么。

> 两种最常见的方法是使用 Webpack 和它的[捆绑包加载器](https://github.com/webpack-contrib/bundle-loader)或者 ECMAScript 动态`import()`提议，目前是[第三阶段](https://tylermcginnis.com/videos/ecmascript/)。我抓住任何不使用 webpack 的机会，所以在这篇文章中我们将使用 dynamic `import()`。

如果你熟悉 ES 模块，你会知道它们是完全静态的。这意味着您必须在编译时指定导入和导出的内容，而不是在运行时。这也意味着您不能基于某些条件动态地导入一个模块。需要在文件的顶部声明，否则它们会抛出一个错误。

```
if (!user) {
  import * as api from './api' // 🙅‍♀️🚫. "import' and 'export' may only appear at the top level"
} 
```

现在，如果`import`没有**有**是静态的呢？意思是如果上面的代码有效呢？那会给我们带来什么好处？首先，这意味着我们可以按需加载某些模块。这将是非常强大的，因为它将使我们更接近只下载用户需要的代码的愿景。

```
if (editPost === true) {
  import * as edit from './editpost'

  edit.showEdtior()
} 
```

假设`editpost`包含一个非常大的富文本编辑器，我们会确保在用户真正准备好使用它之前不会下载它。

> 另一个很酷的用例是遗留支持。您可以推迟下载某些代码，直到您确定用户的浏览器本身没有这些代码。

好消息是(我之前已经提到过)。这种类型的功能确实存在，它受到 Create React App 的支持，目前正处于 ECMAScript 流程的第 3 阶段。不同之处在于，您没有像通常那样使用`import`，而是像使用一样使用**，这是一个函数，它返回一个承诺，一旦模块被完全加载，该承诺就与模块一起解决。** 

```
if (editPost === true) {
  import('./editpost')
    .then((module) => module.showEditor())
    .catch((e) => )
} 
```

很棒，对吧？

既然我们知道了如何动态导入模块，下一步就是弄清楚如何在 React 和 React Router 中使用它。

当使用 React 进行代码拆分时，我们需要问自己的第一个(也可能是最大的)问题是**我们应该在哪里进行拆分？通常有两个答案。**

> 1.  Split at the routing level. 2) Split at the component level.

更常见的方法是在路由级别进行分割。你已经把你的应用程序分割成不同的路径，所以在其上添加代码分割感觉很自然。这实际上会是什么样子呢？

让我们从一个基本的 React 路由器示例开始。我们将有三条路线，`/`，`/topics`，`/settings`。

```
import React, { Component } from 'react'
import {
  BrowserRouter as Router,
  Route,
  Link,
} from 'react-router-dom'

import Home from './Home'
import Topics from './Topics'
import Settings from './Settings'

class App extends Component {
  render() {
    return (
      <Router>
        <div>
          <ul>
            <li><Link to='/'>Home</Link></li>
            <li><Link to='/topics'>Topics</Link></li>
            <li><Link to='/settings'>Settings</Link></li>
          </ul> 
          <hr />

          <Route exact path='/' component={Home} />
          <Route path='/topics' component={Topics} />
          <Route path='/settings' component={Settings} />
        </div>
      </Router>
    )
  }
}

export default App 
```

现在，假设我们的`/settings`路线非常拥挤。它包含一个丰富的文本编辑器，超级马里奥兄弟的原始副本，以及盖伊·菲力的高清图像。我们不希望用户在不在`/settings`路线上时不得不下载所有的内容。让我们使用动态导入和 React 的知识来对`/settings`路线进行代码拆分。

就像我们在 React 中解决任何问题一样，我们来做一个组件。我们就叫它`DynamicImport`。`DynamicImport`的目标是动态加载一个模块，然后，一旦它被加载，就将该模块传递给它的`children`。

```
const Settings = (props) => (
  <DynamicImport load={() => import('./Settings')}>
    {(Component) => Component === null
      ? <Loading />
      : <Component {...props} />}
  </DynamicImport> ) 
```

上面的代码告诉我们关于`DynamicImport`的两个重要细节。首先，它将接收一个`load` prop，当它被调用时，将使用我们前面介绍的动态导入语法动态导入一个模块。其次，它将接收一个函数作为它的`children`，这个函数需要被导入的模块调用。

在我们深入到`DynamicImport`的实现之前，让我们考虑一下我们可能如何实现它。我们需要做的第一件事是确保调用`props.load`。这将返回给我们一个承诺，当它解决，应该有模块。然后，一旦我们有了模块，我们需要一种方法来引起重新渲染，这样我们就可以调用`props.children`传递给它那个模块。如何在 React 中重新渲染？通过设置状态。通过将动态导入的模块添加到`DynamicImport`的本地状态，我们遵循与我们习惯的 React 完全相同的过程——获取数据—>设置状态—>重新呈现。除了这次不是“获取数据”，而是“导入模块”。

首先，我们给`DynamicImport`添加一些初始状态。`component`将最终成为我们动态导入的组件。

```
class DynamicImport extends Component {
  state = {
    component: null
  }
} 
```

现在，我们需要调用`props.load`。这将返回给我们一个承诺，当它解决，应该有模块。

```
class DynamicImport extends Component {
  state = {
    component: null
  }
  componentWillMount () {
    this.props.load()
      .then((component) => {
        this.setState(() => ({
          component
        }))
      })
  }
} 
```

这里有一个小问题。如果我们动态导入的模块使用的是 ES 模块(导出默认值)，那么它将有一个. default 属性。如果模块使用的是 commonjs (module.exports)，就不会。让我们修改代码来适应这种情况。

```
this.props.load()
  .then((component) => {
    this.setState(() => ({
      component: component.default ? component.default : component
    }))
  }) 
```

既然我们正在动态导入模块并将其添加到我们的状态中，我们需要做的最后一件事就是弄清楚`render`方法是什么样子的。如果你还记得，当使用`DynamicImport`组件时，它看起来像这样

```
const Settings = (props) => (
  <DynamicImport load={() => import('./Settings')}>
    {(Component) => Component === null
      ? <Loading />
      : <Component {...props} />}
  </DynamicImport> ) 
```

注意，我们传递给它一个函数，因为它是`children` prop。这意味着我们需要调用`children`传递的是`component`状态。

```
class DynamicImport extends Component {
  state = {
    component: null
  }
  componentWillMount () {
    this.props.load()
      .then((component) => {
        this.setState(() => ({
          component: component.default ? component.default : component
        }))
      })
  }
  render() {
    return this.props.children(this.state.component)
  }
} 
```

🕺.坚实。现在，每当我们想要动态导入一个模块时，我们可以将它包装在`DynamicImport`中。如果我们对所有的路由都这样做，我们的代码看起来会像这样。

```
import React, { Component } from 'react'
import {
  BrowserRouter as Router,
  Route,
  Link,
} from 'react-router-dom'

class DynamicImport extends Component {
  state = {
    component: null
  }
  componentWillMount () {
    this.props.load()
      .then((component) => {
        this.setState(() => ({
          component: component.default ? component.default : component
        }))
      })
  }
  render() {
    return this.props.children(this.state.component)
  }
}

const Home = (props) => (
  <DynamicImport load={() => import('./Home')}>
    {(Component) => Component === null
      ? <p>Loading</p>
      : <Component {...props} />}
  </DynamicImport> )

const Topics = (props) => (
  <DynamicImport load={() => import('./Topics')}>
    {(Component) => Component === null
      ? <p>Loading</p>
      : <Component {...props} />}
  </DynamicImport> )

const Settings = (props) => (
  <DynamicImport load={() => import('./Settings')}>
    {(Component) => Component === null
      ? <p>Loading</p>
      : <Component {...props} />}
  </DynamicImport> )

class App extends Component {
  render() {
    return (
      <Router>
        <div>
          <ul>
            <li><Link to='/'>Home</Link></li>
            <li><Link to='/topics'>Topics</Link></li>
            <li><Link to='/settings'>Settings</Link></li>
          </ul> 
          <hr />

          <Route exact path='/' component={Home} />
          <Route path='/topics' component={Topics} />
          <Route path='/settings' component={Settings} />
        </div>
      </Router>
    )
  }
}

export default App 
```

我们怎么知道这实际上是工作和代码分割我们的路线？如果你用 Create React App 创建的应用运行`npm run build`，你会看到我们的应用被拆分了。

[![code splitting in action](img/34d555df8052fdf8c590c51a6221a349.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7_pzfVec--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tylermcginnis.com/static/e2e31d0c32d04fe25ee03fb87a15b0ad-fed13.png)

每个`chunk`都是我们 app 里的每个动态`import()`。

> 你已经走到这一步了。霹雳舞🕺👩‍🎤

还记得之前我们讨论过有两种主要的方法来对你的应用进行代码拆分吗？我们有这个方便的小指南。

> 1.  Split at the routing level. 2) Split at the component level.

到目前为止，我们只讨论了路由级别的分割。这是很多人停下来的地方。仅在路由级别进行代码拆分就像刷牙但从不使用牙线一样。你的牙齿基本上是干净的，但还是会有蛀牙。

不要把代码分割看作是按路径分割你的应用程序，你应该把它看作是按组件分割你的应用程序(毕竟，只是组件)。如果您有一个存在于模态中的富文本编辑器，那么即使模态从未打开，仅按路径分割仍将加载编辑器。

在这一点上，这更像是你大脑中需要发生的范式转变，而不是任何新知识。你已经知道如何用`import()`动态导入模块，现在你只需要弄清楚你的应用程序中的哪些组件你可以推迟下载，直到你的用户需要它们。

> 如果我没有提到 [React Loadable](https://github.com/thejameskyle/react-loadable) 就结束这篇文章，那我就太傻了。它是“用动态导入加载组件的高阶组件”。本质上，它所做的就是把我们在这篇文章中谈到的所有东西都打包成一个漂亮的小 API。它甚至可以处理一些我们没有提到的边缘情况，比如错误处理和服务器端渲染。如果你想要一个简单的，开箱即用的代码分割解决方案，请查看它。

* * *

这篇文章最初发表在 TylerMcGinnis.com 大学，是他们 T2 反应路由器课程的一部分。