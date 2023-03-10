# Redux-Saga 轻松模式分析

> 原文：<https://dev.to/goshakkk/analytics-on-easy-mode-with-redux-saga>

*本帖最初发表于[goshakk . name](https://goshakkk.name/analytics-easy-redux-saga/)T3】*

跟踪你的用户实际上是如何使用你的应用程序是非常有用的。

为了做到这一点，你*可以*把`window.ga(...)`调用放在任何地方:动作创建者或者你的组件内部。

但是这种方法有没有让你觉得很美呢？

```
class SignUpForm extends Component {
  handleSubmit = () => {
    // fire a redux action
    this.props.doSignUp(data).then(() => {
      window.ga('track', 'CompleteRegistration');
    });
  };
}

class AddTodoForm extends Component {
  handleSubmit = () => {
    // fire a redux action
    this.props.createTodo(data).then(() => {
      window.ga('track', 'CreateTodo');
    });
  };
} 
```

你可以看到它的走向。

你能做得更好吗？

有了 [Redux-saga](http://redux-saga.js.org) ，就可以了。您可以将所有与分析相关的行为(或者大部分行为)放在一个单独的文件中。它将“监听某些 Redux 操作，并自动跟踪您的分析服务。

但是首先，Redux-saga 是关于什么的？在其最简单的形式中，*一个 saga* ，Redux-saga 的 unif，是这样一个函数:

```
function* aSaga() {
  while (true) {
    const action = yield take('ACTION_NAME');
    // do something
  }
} 
```

它等待某个动作发生，然后做某件事——无论你想做什么。之后，它会等待另一个动作发生，然后循环往复。

你可以制作更多更复杂的传奇，但这是一个很好的开始！

*(现在，如果你想知道这个`function*`和`yield`是什么——它们与 ES6 发电机功能有关。我[有一篇博文](/javascript-generators-understanding-sample-use-cases/)会更详细地解释它们。)*

一个更实际的例子是:

```
// analytics-sagas.js

function* registrationAnalytics() {
  while (true) {
    yield take('USER_SIGNED_UP');
    window.ga('track', 'CompleteRegistration');
  }
  }

function* todoAnalytics() {
  while (true) {
    yield take('ADD_TODO');
    window.ga('track', 'CreateTodo');
  }
  }

sagaMiddleware.run(registrationAnalytics);
sagaMiddleware.run(todoAnalytics);

export [
  registrationAnalytics,
  todoAnalytics,
];

// when creating redux store

import analyticsSagas from './analytics-sagas.js';

// ...

analyticsSagas.forEach(saga => sagaMiddleware.run(saga)); 
```

看看这段代码。

分析的全部功能现在都包含在一个文件中。

我们可以“在 Redux 动作发生时从它们中获取信息”,而不是调用我们组件中的分析。

## 注意事项

由于 sagas 只能响应 Redux 操作，所以在你的应用中可能还有一些地方，你可以在组件中直接内联分析调用。然而，即使如此，您仍然比内联一切要好。

## 传说的其他用途

*   [惰性注册模式](https://goshakkk.name/lazy-auth-redux-saga-flow/)。

## 参考文献

*   [了解 ES6 发电机及其使用案例](https://goshakkk.name/javascript-generators-understanding-sample-use-cases/) ![](img/ba0acb4e58bd74f7e230703aaead0532.png)

* * *

如果你喜欢这里的内容，请订阅我的博客，确保你不会错过我下一篇关于 React 和 Redux 的文章。