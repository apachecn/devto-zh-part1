# 用 React 和 Redux 获取 Github

> 原文：<https://dev.to/createdd/fetching-github-with-react-and-redux-1np>

[![](img/fc4bca86e4f5882d89b8e87ebaa0713d.png)](https://unsplash.com/photos/6IxGFVz0wPM) 
*照片由奥斯曼·拉纳[在 Unsplash 上](https://unsplash.com/photos/6IxGFVz0wPM)*

就像生活中的所有事情一样，只有实践才能让你在某个领域变得优秀。因此，我决定使用 React 和 Redux 创建另一个应用程序。这次我想用 Redux 重点介绍异步动作流，它与同步调度流程有一点不同。

[Github 回购可在此处获得…](https://github.com/DDCreationStudios/fetchingReactRedux)

* * *

> ## "Aim high and don't listen to those who tell you you can't do it. Life is too short to think about small things. Tim Ferris

## 我要建造什么

我将构建一个简单的应用程序，通过键入 Github 用户名从 Github 获取存储库:

[![gif](img/3097c2d96b07a44fd59315078fa80001.png)T2】](https://camo.githubusercontent.com/c0b94d5f36a091f0c2e915cec5441045c5aca8ec/687474703a2f2f672e7265636f726469742e636f2f586c6b36696a4a7769432e676966)

## 建筑过程

为了快速启动配置，我使用了 Cory House 的 [React Slingshot 样板](https://github.com/coryhouse/react-slingshot)。它在整个构建过程中提供了良好的林挺和反馈。

首先，我从定义基本的 React 组件开始。我使用了提供的结构，并将其应用于主页和 about 页面。为了跨越路由，我还使用了提供的 React 路由器功能，因为它简单快捷。

下一步是添加一些基本的样式。我想使用 Material-UI，但很快意识到我必须深入框架。在与 bug 相处了几分钟后，我决定继续使用我过去用过的[materialiceacss](http://materializecss.com/getting-started.html)。它提供了很棒的文档和简单的 CSS 组件。这是我最喜欢使用的 CSS 框架。

## Redux 过程

之后，我连接了一个基本的 Redux 流，提供了一个存储、动作和一个 reducer。在 Redux 中异步工作的一种方式是使用 [redux-thunk](https://github.com/gaearon/redux-thunk) 。我选择这种方式是因为它既快又可靠。(我不想处理 Redux-Saga，因为我需要更多关于承诺的知识)

来自 redux-thunk 的文档:

> Redux Thunk 中间件允许您编写返回函数而不是动作的动作创建器。thunk 可用于延迟动作的调度，或者仅在满足特定条件时才进行调度。内部函数接收存储方法 dispatch 和 getState 作为参数。

这就是全部的魔力。返回一个函数而不是一个动作。它允许在 http 调用(或任何调用)后等待应答，并在收到数据后调度操作。

代码看起来像:

```
//Action
import axios from 'axios';
import * as types from './actionTypes';

export function loadReposSuccess(repos) {
    return {
        type: types.LOAD_REPOS_SUCCESS,
        repos
    };
}

export function loadRepos() {
    return function(dispatch) {
        return axios
            .get('https://api.github.com/users/DDCreationStudios/repos')
            .then(repos => {
                dispatch(loadReposSuccess(repos.data));
                console.warn(repos.data);
            })
            .catch(err => {
                throw err;
            });
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

```
//index.js
import React from 'react';
import { render } from 'react-dom';
import { Router, browserHistory } from 'react-router';
import 'materialize-css/dist/css/materialize.min.css';
import { Provider } from 'react-redux';

import routes from './routes';
import configureStore from './store/configureStore';
import { loadRepos } from './actions/reposAction';

const store = configureStore();
store.dispatch(loadRepos());

render(
    <Provider store={store}>
        <Router history={browserHistory} routes={routes} />
    </Provider>,
    document.getElementById('app')
); 
```

Enter fullscreen mode Exit fullscreen mode

```
//reducer
import * as types from '../actions/actionTypes';

export default function reposReducer(state = [], action) {
    switch (action.type) {
        case types.LOAD_REPOS_SUCCESS: {
            return action.repos;
        }
        default:
            return state;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 附带说明:我使用了 [axios](https://www.npmjs.com/package/axios) ，因为我最初认为我会创建一个更大的应用程序。一个简单的“获取”方法也足够了。:)

## 用 Redux 构建搜索功能

这稍微复杂一点，因为我需要根据另一个用户动作进行提取。但这也是 Redux 如此伟大的原因。

关键的事情是调节 index.js 中的存储流，因为我想订阅存储，并且只在状态发生某种变化时才调度一个动作。我找到了“handleChange”辅助函数作为解决方案:

```
//index.js
import React from 'react';
import { render } from 'react-dom';
import { Router, browserHistory } from 'react-router';
import 'materialize-css/dist/css/materialize.min.css';
import { Provider } from 'react-redux';

import routes from './routes';
import configureStore from './store/configureStore';
import { loadRepos } from './actions/reposAction';

let currentValue;
function handleChange() {
    let previousValue = currentValue;
    currentValue = store.getState().user;

    if (previousValue !== currentValue) {
        store.dispatch(loadRepos(store.getState().user));
    }
}

const store = configureStore();
store.dispatch(loadRepos(store.getState().user));
store.subscribe(handleChange);

render(
    <Provider store={store}>
        <Router history={browserHistory} routes={routes} />
    </Provider>,
    document.getElementById('app')
); 
```

Enter fullscreen mode Exit fullscreen mode

现在，只有当用户在存储中的状态发生变化时，才会调用数据提取。哈瑞卡！

然后我相应地改编了其他文件:

```
//reducer index.js

import { combineReducers } from 'redux';

import repos from './reposReducer';
import user from './userReducer';

const rootReducer = combineReducers({
    repos,
    user
});

export default rootReducer; 
```

Enter fullscreen mode Exit fullscreen mode

```
//initialState.js
export default {
    repos: [],
    user: 'DDCreationStudios'
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
//updated repo reducer
import * as types from '../actions/actionTypes';
import initialState from './initialState';

export default function reposReducer(state = initialState.repos, action) {
    switch (action.type) {
        case types.LOAD_REPOS_SUCCESS: {
            return action.repos;
        }
        default:
            return state;
    } 
```

Enter fullscreen mode Exit fullscreen mode

```
//user reducer
import * as types from '../actions/actionTypes';
import initialState from './initialState';

export default function userReducer(state = initialState.user, action) {
    switch (action.type) {
        case types.LOAD_USER_SUCCESS: {
            return action.user;
        }
        default:
            return state;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
//user action
import axios from 'axios';
import * as types from './actionTypes';

export function loadUser(user) {
    return {
        type: types.LOAD_USER_SUCCESS,
        user
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

```
//updated repo action
import axios from 'axios';
import * as types from './actionTypes';

export function loadReposSuccess(repos) {
    return {
        type: types.LOAD_REPOS_SUCCESS,
        repos
    };
}

export function loadRepos(user) {
    return function(dispatch) {
        return axios
            .get(`https://api.github.com/users/${user}/repos`)
            .then(repos => {
                dispatch(loadReposSuccess(repos.data));
                console.log("receiving following data: "+repos.data);
            })
            .catch(err => {
                throw err;
            });
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

```
//actionTypes
export const LOAD_REPOS_SUCCESS = 'LOAD_REPOS_SUCCESS';
export const LOAD_USER_SUCCESS = 'LOAD_USER_SUCCESS'; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

就是这样！

* * *

连接相应的容器组件，在本例中是“HomePage”，它工作得很好:

```
// HomePage.js
import React, { Component } from 'react';
import PropTypes from 'prop-types';
import { bindActionCreators } from 'redux';
import { connect } from 'react-redux';

import * as userActions from '../../actions/userAction';
import Repo from './Repo';
import Searchbar from './Searchbar';

class HomePage extends Component {
    constructor(props) {
        super(props);
        this.state = {
            user: ""
        };
        this.updateSearch = this.updateSearch.bind(this);
        this.saveSearch = this.saveSearch.bind(this);
    }

    updateSearch(e) {
        let user = e.target.value;
        return this.setState({ user: user });
    }

    saveSearch(e) {
        e.preventDefault();
        this.props.actions.loadUser(this.state.user);
    }

    repoRow(repo, index) {
        return (
            <div key={index}>
                <Repo key={repo.id} repo={repo} />
            </div>
        );
    }

    render() {
        return (
            <div className="container">
                <Searchbar
                    user={this.state.user}
                    onChange={this.updateSearch}
                    onSave={this.saveSearch}
                />
                {this.props.repos.map(this.repoRow)}
            </div>
        );
    }
}

HomePage.propTypes = {
    repos: PropTypes.array.isRequired,
    user: PropTypes.string.isRequired,
    actions: PropTypes.object.isRequired
};

function mapStateToProps(state) {
    return {
        repos: state.repos,
        user: state.user
    };
}

function mapDispatchToProps(dispatch) {
    return {
        actions: bindActionCreators(userActions, dispatch),
    };
}

export default connect(mapStateToProps, mapDispatchToProps)(HomePage); 
```

Enter fullscreen mode Exit fullscreen mode

## 结果

查看我的 Github Repo 也可以看到其他文件。一定要理解基本的 React，以便在我的例子中使用 power Redux。

也可以在 Youtube 上看到整个项目的延时。根据 WakaTime 的说法，我花了 13 个小时在代码库上，整个录音超过 1 个小时。:)

[![screenshot](img/27382c2e9af9c98b796bc9c6b72b995a.png)T2】](https://www.youtube.com/watch?v=3U5mD-iXH1U)

如果你从这篇文章中有所收获，请告诉我你的意见或想法。请确保关注更多内容:)