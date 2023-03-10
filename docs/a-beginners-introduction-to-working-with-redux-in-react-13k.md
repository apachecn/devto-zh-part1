# 在 react 中使用 redux 的初学者入门

> 原文：<https://dev.to/aurelkurtula/a-beginners-introduction-to-working-with-redux-in-react-13k>

在我的另一篇文章[redux 简介](https://dev.to/aurelkurtula/introduction-to-redux-1g3)中介绍了 Redux 的基础知识之后，下一个合乎逻辑的步骤是说明 react 组件如何连接到 Redux 存储。

使这两种技术协同工作成为可能的关键包是`react-redux`。

要轻松启动这个项目，您应该全局安装`create-react-app`包，如果您没有，那么快速安装它，如下所示:

```
npm install -g create-react-app 
```

Enter fullscreen mode Exit fullscreen mode

然后，要创建一个全功能的初学者应用程序，只需像这样使用上面的包:

```
create-react-app name-your-app-what-ever-you-like 
```

Enter fullscreen mode Exit fullscreen mode

以这种方式开始的好处是，所有样板配置——与实际的 react 应用程序无关，只是它如何运行——已经为我们配置好了。

然后，您可以使用`npm start`启动应用程序，它将在端口`3000`运行您的应用程序。

**说了这么多**如果你从未使用过 react，那么这不是适合你的教程。另外，如果你以前没有玩过 redux，那么我强烈推荐你先浏览一下我的另一个关于 redux 的教程。

## 我们开始吧

我学习新事物的最好方法是尽可能少用文件。考虑到这一点，我们将使用`src/index.js`文件。这不是推荐的工作方式，因为显而易见的原因是模块化，将代码分解到不同的文件中是保持代码整洁和可维护的方法之一。但对我们来说，这样更好。

在本教程的最后，我会提供我们在`index.js`中使用的代码的链接，另外，我会谈到我们如何重构代码，使其更加模块化。

### 我们需要安装的软件包

react 需要的所有东西都是通过`create-react-app`命令安装的，除此之外我们需要安装的是下面的包`redux`和`react-redux`。我们可以通过跑步做到这一点

```
npm i redux react-redux 
```

Enter fullscreen mode Exit fullscreen mode

或者如果你使用`yarn`

```
yarn redux react-redux 
```

Enter fullscreen mode Exit fullscreen mode

虽然`redux`模块不需要解释，但是`react-redux`是一个连接 react 和 redux 的模块。

如上所述，打开`src/index.js`，删除那里的内容，让我们开始导入我们的包。

```
import ReactDOM from 'react-dom';
import React, { Component } from 'react';    
import { Provider, connect } from 'react-redux';
import { createStore, compose  } from 'redux'; 
```

Enter fullscreen mode Exit fullscreen mode

前两个已经被我们上面运行的应用程序创建者安装了。

### 使用 redux

**reducer**是监听分派给 redux 的动作并返回存储状态的不可变版本的函数。

当应用程序第一次加载时，reducer 被触发但没有动作，所以它返回初始状态。我们希望初始状态是一个空数组(如第一行的`state`参数所指定的)。然后，如果触发了类型为`CREATE_TODO`的动作，reducer 返回一个新状态，将动作的结果添加到数组中。

```
const todos =  (state = [], action) => {
  switch (action.type) {
    case 'CREATE_TODO':
    return state.concat([action.text])
    default:
      return state;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们使用`redux`提供的`createStore`方法创建**商店**。它接受三个可能的参数:缩减器、预加载状态和增强器(这些在 [redux 文档](https://redux.js.org/docs/api/createStore.html)中有解释)，只有第一个参数是必须的。

```
const store = createStore(
  todos,
  undefined,
  compose(
    window.devToolsExtension ? window.devToolsExtension() : f => f
  )
) 
```

Enter fullscreen mode Exit fullscreen mode

因为我们已经在 reducer 中指定了状态，所以我们将第二个参数设置为`undefined`，但是，如果您将它设置为一个空数组或`['item one']`，这将仅仅意味着 reducer 将使用它作为初始状态(这对于预加载您可能从 API 中检索的数据非常有用)。

我们使用的增强器(第三个参数)只允许我们的应用程序与 redux 浏览器扩展进行交互(如果您没有安装，您可以在这里获得更多信息)。它对实际的应用程序没有影响，它只是一个帮助开发人员的工具。

## 使用 redux 存储在 react 组件中

一个非常基本的 react 设置如下所示:

```
class App extends Component {
  render() {
    return (
      <h1>Hello world</h1>
    );
  }
}
ReactDOM.render(
    <App />, 
  document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

返回`Hello World`的 react 组件呈现在页面上，位于 id 为`root`的元素内。

目前，我们的 react 组件不知道 redux 存储。

为了建立连接，我们必须利用`react-redux`模块，它为我们提供了两个额外的组件`Provider`和`connect()`，这两个组件都允许 react 与 redux 进行交互。

顾名思义，`Provider`为我们的整个 react 应用程序提供存储，`connect`使每个 react 组件能够连接到所提供的存储。

记住，我们已经在我们的`src/index.js`文件中导入了这两个方法。

```
import { Provider, connect } from 'react-redux'; 
```

Enter fullscreen mode Exit fullscreen mode

从 [react-redux 文档](https://github.com/reactjs/react-redux/blob/master/docs/api.md#connectmapstatetoprops-mapdispatchtoprops-mergeprops-options)中我们了解到:

> `<Provider store>`使 Redux 存储对组件层次结构中的`connect()`调用可用

让我们开始吧。让我们让 Redux 存储对`connect()`可用，然后让我们的 react 组件访问该存储。

```
class App extends Component {
  //...
}
const MyApp = connect( state => ({
    todos: state
  }), { createTodo })(App);

ReactDOM.render(
  <Provider store={store}>
    <MyApp />
  </Provider>, 
  document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

`MyApp`是我们的`App`组件，其额外的好处是在其状态中注入存储和操作。

同样，`Provider`获取存储并将其传递给`connect()`,`connect()`将其传递给 react 组件。

#### `connect()`到底在做什么？

**T2`connect([mapStateToProps], [mapDispatchToProps], [mergeProps], [options])`**

> [ `connect()` is]是一个高阶的 React 组件类，它将状态和动作创建者传递到从所提供的参数派生的组件中。- [来自文件](https://github.com/reactjs/react-redux/blob/master/docs/api.md)

```
const MyApp = connect( state => ({
    todos: state
  }), { createTodo })(App); 
```

Enter fullscreen mode Exit fullscreen mode

第一个参数`mapStateToProps`，获取状态(由`Provider`提供)，分配一个变量名`todos`，并将其传递给组件的 props。

下一个参数`[mapDispatchToProps]`，也将我们的`createTodo`动作传递给组件 props。`createTodo`是一个返回 reducers 监听的对象的函数。

```
const createTodo = (text)=>{
  return {
    type: 'CREATE_TODO',
    text
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

(同样，我们在之前的[Redux 简介](https://dev.to/aurelkurtula/introduction-to-redux-1g3)教程中已经介绍过了)

## 使用 react 组件

现在，我们可以从 react `App`组件访问 redux 存储状态。让我们最终从那里与商店互动。

```
class App extends Component {
  _handleChange = e => {
    e.preventDefault()
    let item = e.target.querySelector('input').value;
    this.props.createTodo(item);
  }
  render() {
    return (
      <div>
      <form onSubmit={this._handleChange}>
        <input type="text" name="listItem" />
       <button type="submit">button</button>
       </form>
        <br />
        {this.props.todos.map((text, id) => (
          <div key={id}>
              {text}
          </div>
        )) }
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

先来关注一下`render()`的方法。我们退回一份表格。提交后`_handleChange`方法被触发。从那里`createTodo`行动被派遣。

再往下，我们遍历`todos`数组(我们在`connect()`组件中构造的)并在页面上呈现它们。

注意:每当我们遍历一个列表来呈现值时，react 要求我们提供一个惟一的键，否则我们会得到一个警告:`Warning: Each child in an array or iterator should have a unique "key" prop.`[文档](https://reactjs.org/docs/lists-and-keys.html#keys)解释了为什么 react 要求向每个元素传递惟一的键:

> 关键字有助于识别哪些项目已经更改、添加或删除。应该给数组内部的元素赋予键，以给元素一个稳定的标识

仅此而已。

我们已经创建了一个简单的 todo 列表，可以在其中向 redux store 添加项目，并从 redux store 向 react 组件显示这些项目。

在本教程和 Redux 简介之间，你可以在此基础上添加其他功能，如删除、存档和编辑。这一额外功能的所有重担都将由 redux reducers 和 actions 来承担。在 react `App`组件中，只需要添加几个按钮来触发额外的动作。

## 走向模块化

到目前为止，我们介绍的所有代码都放在一个文件中，即`src/index.js`。我把文件放在这里

在一个适当的应用程序中，这段代码将被模块化成单独的文件。有一种方法可以做到这一点。代码是相同的，我们只是利用了 ES6 的导入/导出功能:

**在`src/index.js`里**

```
import React from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';
import store from './store';
import MyApp from './App';
ReactDOM.render(
  <Provider store={store}>
    <MyApp />
  </Provider>, 
  document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

**在`src/App.js`里**

```
import React, { Component } from 'react';
import { connect } from 'react-redux';
import { createTodo } from './actions/todos';
class App extends Component {
    // exact same code
}
export default connect( state => ({
    todos: state.todos
  }), { createTodo })(App); 
```

Enter fullscreen mode Exit fullscreen mode

**在`src/actions/todos.js`里**

这是所有动作(如`deleteTodo`)要去的地方，但我们只有一个:

```
export function createTodo(text){
  return {
    type: 'CREATE_TODO',
    text
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**在`src/store.js`里**

```
import { combineReducers } from "redux"; 
import { createStore, compose } from 'redux';   
import todos from './reducers';
const  rootReducer =  combineReducers({
  todos
})
export default createStore(
  rootReducer,
  undefined,
  compose(
    window.devToolsExtension ? window.devToolsExtension() : f => f
  )
) 
```

Enter fullscreen mode Exit fullscreen mode

**在`src/reducers/index.js`里**

如果我们有一个以上的减速器，我们会像在 Redux 教程的[介绍中那样使用`combineReducers`模块，但是现在，我们只是在这里转移我们的一个减速器，就像这样:](https://dev.to/aurelkurtula/introduction-to-redux-1g3)

```
export default (state = [], action) => {
  switch (action.type) {
    case 'CREATE_TODO':
    return state.concat([action.text])
    default:
      return state;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

感谢阅读！