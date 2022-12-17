# Paradux 简介:一个用于动态添加缩减器的 Redux 助手

> 原文：<https://dev.to/antjanus/introducing-paradux-a-redux-helper-for-dynamically-adding-reducers>

我是 Redux 的超级粉丝。它超级简单，它的简单赢得了许多人的心。在这个简单的架构之上，我们开始构建更复杂的工具来帮助我们做更复杂的事情。从 Redux Sagas，到各种动作创建器库，以及 reducer 管理器。

Paradux 是一个几乎没有任何代码的瘦助手，它为我们在日益复杂的世界中立足提供了坚实的基础。它是一个 reducer 包装器，允许开发人员在运行时动态地注册和重新注册 reducer。

在 Github 上看看吧！

## 一个小样本

在我进入“为什么”之前，让我们看看 Paradux + Redux 是什么样子的一个例子:

```
// bootstrap.js
import { createStore } from 'redux';
import reducers from './reducers';
import Paradux from 'paradux';

export const paradux = new Paradux(reducers); //default reducers
export let store = createStore(paradux.reducerWrapper()); 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上没有什么不寻常的事情发生。它正如预期的那样工作。你甚至可以传入 combineReducers，它就会工作。当您在其他地方需要该文件并利用导出的 paradux 实例时，魔力就来了。

```
// components/myComponent.js
import { paradux } from '../bootstrap';
export default myComponent extends WhateverFrameworkComponent {
  componentDidMount() {
    this.componentReducerUnsubscribe = paradux.register((state, action) => {
      // logic
      return state;
    });
  }
  componentDidUnmount() {
    this.componentReducerUnsubscribe();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦安装了该组件，闭包缩减器就被添加到 paradux 中，现在作为 Redux 生命周期的一部分运行。它一卸下来，就消失了。

## 为什么？

上面这个微不足道的例子看起来只是把事情复杂化了，对吗？好吧，让我们把它们简化成 Paradux 实际上提供给我们的特性集:

### 自举

不必将所有的 reducer 导入到“根 reducer”或应用程序引导文件中，reducer 可以调用 paradux 实例并注册自己。这使得我们可以将减速器分配到需要的地方，并使组件更加便携。

### 代码分割

现在，代码分割减少器很粗糙，不推荐使用。这意味着应用程序的初始负载包含大量您可能不会用到的逻辑。如果您在一个“登录的”和一个“注销的”用户之间分割 reducerss，您仍然需要交付所有的 reducer。使用 webpack 和 Paradux，您可以轻松地进行代码拆分:

```
// sample route config file
import paradux from './paradux';

export default {
  component: App,
  childRoutes: [
    {
      path: '/admin',
      getComponent(location, cb) {
        require.ensure('./adminReducers', function (require) {
          var adminReducers = require('./adminReducers');
          paradux.register(adminReducers);
        });
      }
    }
  ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 清理友好

我对 Redux 最大的不满之一是，一旦你在那里添加了这个缩减器，它就会一直运行，不断地重新运行，即使它没有被使用。如果它的逻辑与应用程序不再相关，为什么还要保留它呢？Paradux 允许取消注册和移除还原剂。默认情况下，Paradux 在注册时返回一个“注销处理程序”;但是，也可以通过给定的名称空间注册和取消注册 reducers。我们来看这个例子:

```
import paradux from './paradux';

// elsewhere we registered it with paradux.register(adminReducers, 'adminReducers');
export function logoutUser() {
  return (dispatch) => {
    return fetch('/api/logout')
      .then((res) => res.toJSON())
      .then(() => {
        paradux.deregisterByNamespace('adminReducers');

        // admin reducers no longer available or run.
        dispatch(userLoggedOut());
      })
      ;
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

注销用户后，您可能无法访问 reducer 注销处理程序，因此为了安全起见，您可以使用在整个应用程序中共享的处理程序的名称空间。我使用了一个简单的字符串，但是常量 a la Redux 的动作类型也可以工作。

## 有何不可？

为什么不使用 Paradux？也有很多原因！

*   只有一个人支持它
*   没有围绕它构建的工具
*   没有一家大公司对这个想法进行过实战检验
*   这使得状态稍微不太容易预测，因为可以在任何点添加/删除还原剂

## 未来

目前路线图上有几项内容，包括:

*   更好地防止多个客户端试图注册的名称空间删除缩减器
*   具有类似 API 的动态中间件(例如，支持可切换的调试工具集)

最初发布在[我的个人博客](https://antjanus.com/blog/thoughts-and-opinions/introducing-paradux-redux-helper-dynamically-adding-reducers/)上。