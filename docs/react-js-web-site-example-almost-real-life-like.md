# React JS 网站示例(几乎和现实生活一样)。

> 原文：<https://dev.to/svinci/react-js-web-site-example-almost-real-life-like>

几个星期以来，我一直在尝试在我的个人项目中使用 react，但我发现互联网上没有一个例子(我能找到的)能重新组装我在现实生活场景中想要的东西。

异步 HTTP 请求、加载动画、错误页面等。在谷歌的前两页上可以找到一个简明的例子，其中没有一个包含这些内容。

说到这里，我举了一个[例子](https://www.sitepoint.com/how-to-build-a-todo-app-using-react-redux-and-immutable-js/)，这个例子让我走得够远了，我开始在它的基础上进行研究和构建。

## 我们要做什么？

我们将构建一个简单的待办事项列表 web 应用程序。

为此，我们将使用 [rest-api-starter](https://www.npmjs.com/package/rest-api-starter) 在 Node.js 中构建一个非常简单的 REST API，以及一个基于 React 的网站。JS，Redux 和 Bootstrap。

## 按照本教程，我需要做些什么？

首先，安装 Node.js 6，安装一个 IDE 和一个浏览器(在您阅读本文时，您可能已经安装好了)。关于如何安装 Node.js 的说明可以在[这里](https://nodejs.org/en/)找到。

第二，Python 2.7 的安装。如果你在 Mac OS 或者基于 Ubuntu 的系统上，你已经有了它。如何安装 Python 的说明可以在[这里](https://www.python.org/downloads/)找到。

我提供的所有安装、运行和操作的命令都是在 Linux Mint 18 上测试的。他们可能会在 Mac OS 上工作，没有任何问题。如果你在 windows 上工作，我真的很抱歉。

## 我们可以开始编码了吗？

好的，首先，让我们制作目录。

```
$ mkdir todo-api
$ mkdir todo-site 
```

Enter fullscreen mode Exit fullscreen mode

### API 项目

现在，让我们从 API 开始。我们将`cd`转到 API 目录，并运行`npm init`。

```
$ cd todo-api
$ npm init 
```

Enter fullscreen mode Exit fullscreen mode

您可以保留所有默认值。

现在我们有一个节点项目，我们将安装`rest-api-starter`和`uuid`(用于 id 生成等)。

```
$ npm install --save rest-api-starter uuid 
```

Enter fullscreen mode Exit fullscreen mode

现在，`rest-api-starter`需要一个名为`config`的子目录中的微小配置文件。

```
$ mkdir config
$ cd config && touch default.json 
```

Enter fullscreen mode Exit fullscreen mode

`config/default.json`文件看起来应该和下面的一模一样:

```
{
  "app": {
    "http": {
      "port": 8100,
      "host": "0.0.0.0",
      "queue": 10,
      "secret": "",
      "transactionHeader": "X-REST-TRANSACTION"
    },
    "log": {
      "level": "info",
      "transports": [
        {
          "type": "console"
        }
      ]
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们编写我们的 rest API。我们需要 CORS 的支持，以便能够在我们的本地环境和三个处理程序上轻松开发:

*   发布`/todos`:创建一个项目。
*   获取`/todos`:检索所有物品。
*   补丁`/todos/:id`:将一个项目标记为完成或未完成。

此外，为了支持 CORS，应该为每个路径实现一个选项处理程序。因此，我们的`index.js`文件将如下所示:

```
const uuid = require('uuid');
const serveBuilder = require('rest-api-starter').server;
const todos = [];

const router = (app) => {

    app.use(function(req, res, next) {
        res.header("Access-Control-Allow-Origin", "*");
        res.header("Access-Control-Allow-Methods", "GET, POST, PATCH, OPTIONS");
        res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
        next();
    });

    app.options('/todos', (request, response) => response.status(200).send());

    app.post('/todos', (request, response) => {
        const todo = {
            'id': uuid.v4(),
            'isDone': false,
            'text': request.body.text
        };
        todos.push(todo);
        response.send(todo);
    });

    app.get('/todos', (request, response) => {
        response.send(todos);
    });

    app.options('/todos/:id', (request, response) => response.status(200).send());

    app.patch('/todos/:id', (request, response) => {
        let result = null;
        todos.forEach((todo) => {
            if (todo.id === request.params.id) {
                todo.isDone = !todo.isDone;
                result = todo;
            }
        });

        if (!result) {
            response.status(404).send({'msg': 'todo not found'});
        } else {
            response.send(result);
        }
    });

};

serveBuilder(router); 
```

Enter fullscreen mode Exit fullscreen mode

现在，将`"start": "node index.js"`添加到 package.json 文件的`scripts`部分来启动服务器。通过在 API 项目的根目录下运行`npm run start`，您将让您的服务器监听`http://localhost:8100`。

### 工地项目

现在我们将 cd 转到站点项目，并在那里运行一个`npm init`。这里默认也没问题。

```
$ cd todo-site
$ npm init 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们安装我们需要的依赖项:

```
$ npm install --save babel-core babel-loader babel-preset-es2015 babel-preset-react bootstrap jquery superagent webpack react react-dom react-redux redux redux-thunk style-loader css-loader 
```

Enter fullscreen mode Exit fullscreen mode

#### Webpack

我们将使用 webpack 来传输并将所有代码统一到一个名为`bundle.js`的 una 文件中，因此将`"build": "webpack --debug"`和`"serve": "npm run build && python -m SimpleHTTPServer 8080"`添加到我们的 package.json 中的脚本部分会很方便

现在我们需要一个`webpack.config.js`。

```
const webpack = require('webpack');

module.exports = {
    entry: {
        main: './src/app.js'
    },
    output: {
        path: __dirname,
        filename: 'bundle.js'
    },
    module: {
        loaders: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                loader: 'babel-loader',
                query: { presets: [ 'es2015', 'react' ] }
            },
            {
                test: /\.css$/,
                loader: "style-loader!css-loader"
            },
            {
                test: /\.(png|jpg|gif|ttf|svg|woff|woff2|eot)$/,
                loader: "url-loader"
            }
        ]
    },
    plugins: [
        new webpack.ProvidePlugin({
            $: "jquery",
            jQuery: "jquery",
            bootstrap: "bootstrap"
        })
    ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

这个 webpack 配置将所有使用 ES6 和 JSX 的 javascript 文件打包，然后将它们和它们所有的依赖项放在一个名为`bundle.js`的大文件中。

如果需要来自`src/app.js`的任何样式表，它将导入它并将其添加到包中(在样式表的任何导入之后),生成的包脚本将向 HTML 添加一个`<style>`标记。

它还使用`ProvidePlugin`来公开 JQuery 和 bootstrap，所以我们可以忘记导入它们。

#### 样式表

现在，让我们从一些结构开始。让我们在项目的根目录下创建一个名为`css`的目录，并添加下面的`app.css`。

```
@import "../node_modules/bootstrap/dist/css/bootstrap.min.css"; 
```

Enter fullscreen mode Exit fullscreen mode

该样式表只是导入 bootstrap，但是您可以添加自定义样式并导入任何想要的样式表。这应该是项目中所有样式表的入口点。

#### HTML。entrypoint 站点。

然后，我们在项目中创建我们的`index.html`。

```
<!DOCTYPE html>
<html>
    <head>
        Todo List

        <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
    </head>
    <body>
        <div id="app"></div>

        <script src="bundle.js"></script>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

这是一个非常简单的 HTML 文件。它有一个标题、bootstrap 推荐的 viewport、一个 id 为`app`的`div`和我们的包的导入。

那个名为`app`的 div 将是我们的应用程序容器。我们将告诉 react 在那里渲染它的组件。

#### 反应成分

让我们编写 React.js 组件。React 组件是一段独立的代码，它接收一些属性并从这些属性中呈现 HTML。它应该只是反应，一个组件的代码应该不知道 Redux。只是演示。(这一点我怎么强调都不为过)。

在项目的根目录下创建一个名为`src`的目录，并将下面的代码写到名为`components.js`的文件中。

```
import React from 'react';

function Todo(props) {
    const { todo } = props;
    if (todo.isDone) {
        return <del>{todo.text}</del>
    } else {
        return <span>{todo.text}</span>
    }
}

function TodoList(props) {

    const { todos, toggleTodo, addTodo } = props;

    const onSubmit = (event) => {
        event.preventDefault();

        const textInput = document.getElementById('todo-input');

        const text = textInput.value;

        if (text && text.length > 0) {
            addTodo(text);
        }

        textInput.value = '';
    };

    const toggleClick = id => event => toggleTodo(id);

    return (
        <div className='todo-list-container'>
            <div className="panel panel-default">
                <div className="panel-body">
                    <form onSubmit={onSubmit}>
                        <div className="form-group">
                            <label>To Do Text: </label>
                            <input id="todo-input" type='text'
                                   className='todo-input form-control'
                                   placeholder='Add todo' />
                        </div>
                        <button type="submit" className="btn btn-default">Submit</button>
                    </form>
                </div>
            </div>
            {
                todos.length > 0 ?
                    <div className='todo-list list-group'>
                        {todos.map(t => (
                            <a key={t.id}
                                className='todo-list-item list-group-item'
                                onClick={toggleClick(t.id)}>
                                <Todo todo={t} />
                            </a>
                        ))}
                    </div> :
                    <div className="alert alert-info" role="alert">ToDo list is empty.</div>
            }
        </div>
    );
}

function Layout(props) {
    return (
        <div className='container'>
            <div className='row'>
                <div className='col-lg-6 col-lg-offset-3'>
                    <div className='page-header'>
                        <h1>To Do List <small>Keep it organized.</small></h1>
                    </div>
                    {props.children}
                </div>
            </div>
        </div>
    )
}

function ProgressBar(props) {
    const { completed } = props;

    const style = { 'width': completed + '%'};

    return (
        <div className="progress">
            <div className="progress-bar progress-bar-striped active" role="progressbar" aria-valuenow={completed} aria-valuemin='0' aria-valuemax='100' style={style}>
                <span className="sr-only">{completed}% Complete</span>
            </div>
        </div>
    )
}

export function TodoPage(props) {

    const {state, toggleTodo, addTodo, retrieveTodos } = props;

    if (state.error) {
        return (
            <Layout>
                <div className="alert alert-danger" role="alert">{state.error.toString()}</div>
                <input className='retry-button btn btn-default' type='button' value='Retry' onClick={retrieveTodos}/>
            </Layout>
        );
    } else if (state.initialized) {
        return (
            <Layout>
                <TodoList todos={state.todos} toggleTodo={toggleTodo} addTodo={addTodo} />
            </Layout>
        )
    } else {
        retrieveTodos();
        return (
            <Layout>
                <ProgressBar completed="45"/>
            </Layout>
        );
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的表示层。我们导出一个名为`TodoPage`的函数，它使用了一些只在模块内部可用的组件。

这些组件接收应用程序的状态和三个动作:toggleTodo、addTodo、retrieveTodos。组件不知道它们做什么，它们只知道如何调用它们，它们甚至不关心返回值。

注意，组件接收状态和动作，只关心状态如何显示，以及那些动作如何映射到 HTML 事件。

#### API 客户端

现在，让我们使用`superagent`和 ES6 承诺来编写我们的 API 客户端。在项目根目录下创建的名为`src`的目录下，在名为`client.js`的文件中编写以下代码。

```
import * as superagent from "superagent";

export function get() {

    return new Promise((resolve, reject) => {
        superagent.get("http://localhost:8100/todos")
            .end((error, result) => {
                error ? reject(error) : resolve(result.body);
            });
    });

}

export function add(text) {

    return new Promise((resolve, reject) => {
        superagent.post("http://localhost:8100/todos")
            .send({'text': text})
            .end((error, result) => {
                error ? reject(error) : resolve(result.body);
            });
    });

}

export function toggle(id) {

    return new Promise((resolve, reject) => {
        superagent.patch("http://localhost:8100/todos/" + id)
            .end((error, result) => {
                error ? reject(error) : resolve(result.body);
            });
    });

} 
```

Enter fullscreen mode Exit fullscreen mode

该模块导出三个函数:

*   get:在我们的 API 中对`/todos`执行 GET 请求，以检索所有的待办事项。
*   add:在我们的 API 中执行对`/todos`的 POST 请求，以添加一个待办事项。
*   toggle:执行对`/todos/:id`的补丁请求，以更改该项目的`isDone`标志。

#### Redux 动作

让我们来谈谈行动...

在 Redux 中，动作是发送到商店的信息片段。这些有效负载触发对应用程序状态的修改。

动作基本上是 Redux 说“嘿！出了这种事！”。

警告:不是实际的修改，应用程序的状态应该被视为不可变的对象。你永远不要修改状态，而是复制它，改变副本，继续走下去。继续往下看。

动作是通过动作生成器生成的。这些构建器是用一些信息调用并返回动作的函数，动作通过 Redux 提供的`dispatch`函数发送到商店。

一个有趣的概念，对于现实世界的应用程序来说是必要的，就是异步操作。这些实际上不仅仅是一条信息，而是另一个函数，它接收`dispatch`函数作为参数，并在一些异步操作之后，分派另一个动作。我们用一些代码来解释一下。

在`src`目录下的一个名为`actions.js`的文件中编写以下代码。

```
import { get, add, toggle } from './client';

export function addTodo(text) {
    return (dispatch) => {
        add(text)
            .then(get)
            .then((todos) => dispatch(receiveTodos(todos)))
            .catch((err) => dispatch(error(err)));
    };
}

export function toggleTodo(id) {
    return (dispatch) => {
        toggle(id)
            .then(get)
            .then((todos) => dispatch(receiveTodos(todos)))
            .catch((err) => dispatch(error(err)));
    };
}

export function retrieveTodos() {
    return (dispatch) => get()
        .then((todos) => dispatch(receiveTodos(todos)))
        .catch((err) => dispatch(error(err)))
}

function receiveTodos(todos) {
    return {
        type: 'RECEIVE_TODOS',
        payload: todos
    }
}

function error(err) {
    return {
        type: 'ERROR',
        payload: err
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里定义了应用程序的所有行为。

我们的应用程序必须从 API 中检索待办事项，切换并创建它们。这些动作是异步的。

*   addTodo 操作生成器返回一个异步操作，在向 API 提交新的待办事项并再次检索所有待办事项后，该操作将调度`receiveTodos`操作。出错时，它会调度`error`动作。

*   toggleTodo 操作生成器返回一个异步操作，在切换 API 上的待办事项并再次检索所有项目后，该操作将调度`receiveTodos`操作。出错时，它会调度`error`动作。

*   retrieveTodos 操作生成器返回一个异步操作，在从 API 中检索所有待办事项后，该操作将调度`receiveTodos`操作。出错时，它会调度`error`动作。

请注意，这些(不是在这里定义的，我们将看到如何定义)是我们的组件用来处理 HTML 事件的动作。

另外两个动作是普通的动作，它们接收一些数据并返回一个有效载荷。

*   receiveTodos 动作生成器返回类型为`RECEIVE_TODOS`的动作，将检索到的 Todos 作为有效负载。

*   错误动作生成器返回类型为`ERROR`的动作，将接收到的错误作为有效负载。

这听起来可能令人困惑。我认为 Redux 不是一个容易理解的状态管理器，它的概念很难理解，但是如果你将它付诸实践并阅读代码，你会非常喜欢它。

#### Redux 减速器

这就把我们带到了减速器。缩减器是接收应用程序和动作的当前状态的函数。如前所述，动作是说明发生了什么事情的一种方式，缩减器获取该事件/信息，并对状态做它需要做的事情以影响该事件。

基本上，它们接收应用程序的当前状态和被执行的动作(一个事件或某事，例如用户点击),并返回应用程序的新状态。

让我们看看更多的代码。在`src`目录下的一个名为`reducer.js`的文件中编写以下代码。

```
 const init = {'todos': [], 'error': false};

export default function(state=init, action) {
    switch(action.type) {
        case 'RECEIVE_TODOS':
            return {'todos': action.payload, 'error': false, 'initialized': true};
        case 'ERROR':
            return {'todos': [], 'error': action.payload, 'initialized': true};
        default:
            return state;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个缩减器定义了应用程序的初始状态，并负责处理它接收到的动作。

如果它接收的动作是类型`RECEIVE_TODOS`，它返回新的状态，确保`error`为假，`initialized`为真，`todos`包含接收到的 todos。

如果它接收的动作是类型`ERROR`，它返回新的状态，确保`error`包含发生的错误，`initialized`为真，`todos`为空数组。

如果它接收的动作没有处理程序，它只是通过应用程序的当前状态，因为没有应用任何更改。

抱歉，我重复了这么多，但这个概念花了我一段时间:React 组件接收 Redux 的动作生成器，并在 HTML 事件上调用它们。这些事件被分派给 Redux 的 reducers，让它们根据动作提供的信息来处理状态。

#### 容器组件

另一个新概念:容器。容器是组件的一种，它们被称为`Container Components`。它们在 React 组件(只是表示性的组件，对 redux 一无所知)和 redux 的动作和状态之间建立联系。

它们基本上包装了 react 组件，获取状态和动作，并将它们映射到道具。

让我们看看代码。在`src`目录下名为`containers.js`的文件中编写以下代码。

```
import { connect } from 'react-redux';
import * as components from './components';
import { addTodo, toggleTodo, retrieveTodos } from './actions';

export const TodoPage = connect(
    function mapStateToProps(state) {
        return { state: state };
    },
    function mapDispatchToProps(dispatch) {
        return {
            addTodo: text => dispatch(addTodo(text)),
            toggleTodo: id => dispatch(toggleTodo(id)),
            retrieveTodos: () => dispatch(retrieveTodos())
        };
    }
)(components.TodoPage); 
```

Enter fullscreen mode Exit fullscreen mode

它抓取我们的`TodoPage`、我们的动作和状态，并将它们放入道具中，供我们的组件查看。这是所有东西粘合在一起的地方。

#### Web 应用启动

现在让我们进入应用程序入口点。在`src`下的一个名为`app.js`的文件中写下以下代码。

```
import '../css/app.css';

import React from 'react';
import { render } from 'react-dom';
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import { Provider } from 'react-redux';
import reducer from './reducer';
import { TodoPage } from './containers';

const store = createStore(reducer, applyMiddleware(thunk));

document.addEventListener("DOMContentLoaded", function() {

    render(
        <Provider store={store}>
            <TodoPage />
        </Provider>,
        document.getElementById('app')
    );

}); 
```

Enter fullscreen mode Exit fullscreen mode

这个文件正在导入我们的 css 入口点文件、我们的 reducer 和 TodoPage 容器(不是组件，是容器)。

然后，它创建 Redux 存储(基本上就是 te state 所在的位置)。你可能已经注意到我们的 reducer 没有处理我们的任何异步动作，这就是为什么我们把那个`applyMiddleware(thunk)`传递给`createStore`。`redux-thunk`就是这样处理异步动作的。

我们现在等待 DOM 被加载，然后调用 React 的`render`函数。该函数接收一个组件和容器 HTML 元素(这是我们来自 index.html 的 div #应用程序)。

我们传递给`render`函数的组件是一个`Provider`标签，其中*只有一个孩子*(这很重要，它不能有多个孩子)，这是我们的`TodoPage`容器组件。我们顺便将我们的商店传递给`Provider`标签。

#### 你准备好了吗

我们现在可以在站点项目的根目录下运行`npm run serve`，在 API 项目的根目录下运行`npm run start`。现在我们可以参观`http://localhost:8080/`并使用我们的待办事项列表。

## 结论

我发现这对(React，Redux)有一个相当复杂的上升过程，但是一旦你掌握了其中的窍门，应用程序就可以很快地编写出来，代码看起来也很棒。是的，它有时是很多锅炉板，但它看起来很好，它实际上也表现得很好。

我来自 JQuery 世界，然后去了 Angular。JS，现在我动了反应。JS 和 Redux 我其实挺喜欢的。

你可以在这里找到这个例子[的代码。](https://github.com/svinci/react-example)

评论里见！