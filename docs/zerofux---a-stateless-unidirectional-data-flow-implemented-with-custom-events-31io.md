# zero fux——用自定义事件实现的无状态单向数据流

> 原文：<https://dev.to/kahlil/zerofux---a-stateless-unidirectional-data-flow-implemented-with-custom-events-31io>

单向数据流，Flux，Redux，Whateverux 本质上是这样的:

*   有事发生了
*   正在用动作对象描述所发生的事情
*   这个动作对象是通过一个中心点，即调度程序来调度的
*   另一方面，dispatcher 操作与 reducers 相匹配
*   归约器获取动作的信息并返回状态

这允许您以无状态和同步的方式管理 UI 中的交互。

作为一名开发人员，你唯一真正感兴趣的是动作和减少器，其余的只是实现细节。动作和缩减器决定了应用程序的状态。

在下面，我描述了一个非常简单的方法，你可以用自定义事件实现这种类型的状态管理。

## 动作

首先让我们定义什么是动作。一个动作是一个 JavaScript 对象，它有一个带有关键字“type”的必需属性和两个带有关键字“payload”和“error”的可选属性。这里一个动作被定义为一个 TypeScript 接口。

```
{
  type: string;
  payload?: any;
  error?: boolean;
} 
```

Enter fullscreen mode Exit fullscreen mode

我想我们可以同意，JavaScript 社区大多同意 Redux 开创的这个定义，也许不包括`error`属性。那是我从`redux-observable`偷来的。

## 调度员

那么，现在我们有了动作，我们如何通过调度程序来调度它们，调度程序是什么？

我们想使用自定义事件。这些事件通过`dispatchEvent`方法在 DOM 元素上调度。这意味着我们的 dispatcher 是一个 DOM 元素。哪一个真的不重要，但让我们只使用`body`元素，因为该元素存在于任何 web 应用程序中。

```
const dispatcher = document.querySelector('body'); 
```

Enter fullscreen mode Exit fullscreen mode

太好了。现在我们有了调度程序，我们如何调度一个动作呢？这就是自定义事件的由来。我们使用自定义事件，因为它们允许我们添加自定义事件数据(我们将悄悄地称之为动作)。

```
dispatcher.dispatchEvent(
  // The first argument of the Custom Event is 
  // the event name. The event name is the same
  // as the action type.
  // The second argument are options.
  new CustomEvent('SOME_ACTION', {
    // Here goes our custom data, the action object.
    detail: { 
      // Action type and event name are the same.
      type: 'SOME_ACTION', 
      // Here goes the optional data.
      payload: someData, 
      error: false,
    },
  })
) 
```

Enter fullscreen mode Exit fullscreen mode

所以现在所有这些动作都通过定制事件通过 DOM 中的一个点，我们可以将它们匹配到 reducers。

在需要某种状态的组件中，获取组件感兴趣的动作名称数组，并设置事件侦听器。在事件监听器回调中，每个动作匹配一个同名的 reducer 来更新组件状态:

```
// Some example action names.
const ACTIONS = [
  'INCREMENT',
  'DECREMENT',
  'ADD_TEN',
];

...

// As a convention components need a setter and 
// a getter for the state property. 
// That allows you to call a render function or similar
// whenever state is set to a new value.
set state(s) {
  this._state = s;
  // Use lit-html or some other library that efficiently
  // can update DOM in the render function. 
  this.render();
}

get state() {
  return this._state;
}

// This is a method on some component.
setReducers() {
  ACTIONS.forEach(ACTION => {
    if (reducers[ACTION]) {
      // Again we are using the reference to the body
      // element as the dispatcher.
      dispatcher.addEventListener(ACTION, e => {
        // Reducers are kept in an object and matched
        // via action name.
        this.state = reducers[ACTION](e.detail, this.state);
      });
    } else {
      throw new Error(
        `Please add a reducer for the "${ACTION}" action.`
      );
    }
  });   
}

... 
```

Enter fullscreen mode Exit fullscreen mode

## 归零符库

上面的代码有点样板，所以让我们用它做一个简单的库。无通量加无冗余等于零通量:

```
export class ZeroFux {
  constructor(element) {
    if (element) {
      this.dispatcher = element;
    } else {
      this.dispatcher = document.querySelector('body');
    }
  }

  // The dipatch method takes an action argument
  // of the previously defined action type.
  dispatch(action) {
    this.dispatcher.dispatchEvent(
      new CustomEvent(action.type, {
        detail: action,
        // In case you set a custom dispatcher element
        // and want them to bubble.
        bubbles: true,
        // In case your custom dispatcher is in the
        // Shadow DOM and you want them to bubble between
        // the borders or Shadow DOM and regular DOM.
        compose: true,
      })
    )
  }

  // This method takes an array of action types
  // that can influence a component's state,
  // an object with reducers with the same names
  // as the action types and a reference
  // to the component on which we want to set
  // the state propery.
  setReducers(actionTypes, reducers, component) {
    actionTypes.forEach(actionType => {
      if (reducers[actionType]) {
        this.on(actionType, e => {
          const action = e.detail;
          component.state = reducers[actionType](component.state, action);
        });
      } else {
        throw new Error(
          `Please add a reducer for the "${actionType}" action.`
        );
      }
    });
  }
}

export const zeroFux = new ZeroFux(); 
```

Enter fullscreen mode Exit fullscreen mode

🎉tadaa！

如果你想试试的话，它现在就在 Github 和 T2 NPM 上。

你可以在这个[代码笔](https://codepen.io/kahlil/pen/bapoPK)中看到 ZeroFux 的运行。

## 副作用

“啊哈！我们如何管理 ZeroFux 的副作用？”，你可能会问。嗯，实际上有一个简单的方法来处理这个问题。

由于这些自定义事件都是通过 DOM 中的一个点流动的，这个点我们可以通过`zeroFux.dispatcher`访问，我们可以单独监听这些事件，并对某些动作产生效果。

当这些副作用完成了它们正在做的事情时，它们必须自己触发一个动作。这就是我们如何将来自这些副作用的数据同步引入数据流。

这就是你的副作用类的样子:

```
import { zeroFux } from 'zero-fux';

export class SideEffects {
  run() {
    const on = zeroFux.dispatcher.addEventListener;
    on('SOME_ACTION', () => { 
      someApi.doSomethingAsync()
        .then(data => zeroFux.dispatch({
          type: 'SOME_RESPONSE_ACTION',
          payload: data,
        }));
    });

    ...
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在[代码栏](https://codepen.io/kahlil/pen/bapoPK)中查看它的运行情况。

## 结论

这就是一个使用自定义事件的简单、直接的单向数据流实现。

它使用了我在 oddstream 中使用的相同原理，oddstream 是一个用 RxJS 实现的单向数据流库:将“动作流”匹配到 reducers。这只是零依赖，实际上没有代码。

在 Node 中，这可以使用`EventEmitter`来实现。

我认为这种单向数据流的解决方案可以用在任何规模的应用程序中，因为最终你需要管理和考虑的只是动作和减少器，就像任何其他单向数据流解决方案一样。