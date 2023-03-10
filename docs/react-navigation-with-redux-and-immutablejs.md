# react-使用 redux 和 immutable.js 导航

> 原文：<https://dev.to/machadogj/react-navigation-with-redux-and-immutablejs>

上周，我发表了一篇关于将 React Native 与 redux 和 Immutable.js 一起使用的文章，看起来我并不是唯一一个认为第一次做好事情肯定需要一些时间的人。在这种情况下，我将详述如何将反应导航添加到组合中。

#### 反应-导航

react-navigation 的测试版最近已经发布。这个库建立在其他几个导航库的经验之上，也许最著名的是 ex-navigation 和 navigation experimental(来自 RN)。我喜欢这个库是因为它提供了一个简单而强大的 API。它允许以最小的设置使用，但同时它支持与其他库的集成。在这种情况下，我们将探索与`redux`的集成。

#### 需要 redux 带 react-navigation 吗？

在继续阅读本文之前，您应该问问自己是否需要将 react-navigation 与 redux 集成在一起。根据经验，我认为你应该尽量避免走这条路。唯一的原因是，即使没有 redux，您仍然可以获得现成的事件源，并且您仍然可以很容易地看到和调试哪些操作被调度以及每个导航操作前后的状态。

随着您的应用程序变得越来越复杂，我相信在某些情况下，将所有东西都放在 redux 中是值得的。在我的例子中，我试图做以下事情:

*   根据基于应用程序状态的条件推送屏幕。
*   基于当前应用程序状态和导航堆栈推送屏幕。

需要说明的是，没有 redux 也可以做这些事情，但是正如你将会看到的，当与 redux 集成时，我们获得了一些很好的灵活性，并且可以保持代码更少的耦合。

#### React-用 redux 导航

从 react-navigation 文档中我们可以看到，与 redux 集成并不复杂。它们已经为构建 reducers 提供了很好的帮助，并在您的子组件需要使用导航时准备好了道具。下面是从文档中复制并粘贴的代码:

```
const AppNavigator = StackNavigator(AppRouteConfigs);

const appReducer = combineReducers({
  nav: (state, action) => (
    AppNavigator.router.getStateForAction(action, state)
  ),
  ...
});

@connect(state => ({
  nav: state.nav,
}))class AppWithNavigationState extends React.Component {
  render() {
    return (
      <AppNavigator navigation={addNavigationHelpers({
        dispatch: this.props.dispatch,
        state: this.props.nav,
      })} />
    );
  }
}

const store = createStore(appReducer);

class App extends React.Component {
  render() {
    return (
      <Provider store={store}>
        <AppWithNavigationState />
      </Provider>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

还缺少一些东西，我们必须做一些调整才能在 immutable.js 中使用它。

#### 向 mix 中添加不可变的. js

首先，让我们来关心一下减速器。除了需要一个初始状态之外，我们的 reducer 操作不可变的对象，所以下面是我们的 reducer 可能的样子:

```
const initialState = Immutable.fromJS({  
  index: 0,  
  routes: [{  
    routeName: 'init',  
    key: 'init'  
  }]  
});  
const reducer = ( state = initialState, action ) => {  
  return state.merge(Navigator.router.getStateForAction(action, state.toJS()));  
}  
// now you can go ahead an do what is suggested in the docs:  
`const appReducer = combineReducers({  
  nav:reducer  
  //....  
}); 
```

Enter fullscreen mode Exit fullscreen mode

您可能想知道 initalState 是从哪里来的。原来`redux-navigation`用了一个这样的内部状态。因为我们想自己处理它，所以我们必须使用相同的格式(如果我们想使用 redux-navigation 打包的帮助程序，我们就是这样做的)。我从这里取了这个格式:[https://reactnavigation.org/docs/navigators/custom](https://reactnavigation.org/docs/navigators/custom)。

如你所见，我们的初始状态是用`Immutable.fromJS()`方法构造的。在我们告诉路由器的`getStateForAction`助手给我们新的状态之前，我们必须用`.toJS()`将当前状态转换成一个普通的 javascript 对象。最后，我们必须用`state.merge`将它合并到旧的导航状态中。

我们来看看连线:

```
const NavigatorWithState = ({dispatch, nav}) => {  
  return (  
    <Navigator navigation={addNavigationHelpers({  
      dispatch: dispatch,  
      state: nav,  
    })}/>  
  );  
}</pre>

<pre name="33f2" id="33f2" class="graf graf--pre graf-after--pre">export default connect(  
  state => ({  
    nav: state.get('nav').toJS()  
  })  
)(NavigatorWithState); 
```

Enter fullscreen mode Exit fullscreen mode

注意`.toJS()`以确保组件得到一个普通的 javascript 对象。到目前为止，你的导航应该工作得很好，并且你应该能够通过调度 vanilla redux 动作来推送场景。

#### Redux-persist

如果你正在使用`redux-persist`或任何其他机制来持久化你的应用程序的存储，从持久化中`blacklist`导航状态可能不是一个坏主意。这样做的原因是，在你真正让你的用户进入你的应用程序之前，你可能想要运行一些验证和初始化逻辑。如果是这种情况，请避免从本地存储中恢复导航状态。

使用`redux-persist`可以通过`blacklist`选项完成:

```
persistStore(store, {  
  blacklist: ['nav'],  
  storage: AsyncStorage  
}); 
```

Enter fullscreen mode Exit fullscreen mode

#### 在导航中利用 Redux

那么现在你已经完成了整合`react-navigation`和`redux`的旅程，你将能够做什么样的事情呢？

你可以做的事情之一是使用选择器通过 props 为你的组件提供导航状态。例如:

```
const selectors = {  
  currentRoute: state => {  
    let nav = state.get('nav').toJS();  
    return nav.routes[nav.index].routeName;  
  },  
  // selectedItem: state => {....  
  // etc... 
```

Enter fullscreen mode Exit fullscreen mode

然后在您的`connect`中，您可以使用它将状态映射到道具:

```
export default connect(  
  state => ({  
    currentRoute: router.selectors.currentRoute(state)  
  }),  
  dispatch => ({  
    init: () => dispatch(app.actions.init())  
  })  
)(Init); 
```

Enter fullscreen mode Exit fullscreen mode

你可以做的另一件有趣的事情是分派一个动作的新场景。你为什么想这么做？这有助于将一些导航逻辑从组件中分离出来。我喜欢这一点的唯一一点是，在动作创建者方面，与导航相关的一切仍然是普通的对象，没有进一步耦合到导航库(用 react-navigator 等其他导航库很难完成)。

假设您想在 API 调用出现问题时显示一个漂亮的错误屏幕。你可以这样做:

```
const add = (text) => {  
  return async (dispatch) => { //assuming you are using redux-thunk  
    dispatch({type: TODO_ADD_STARTED})  
    try {  
      let result = await todos.add(text);  
      dispatch({type: TODO_ADD_SUCCEEDED, payload: result});  
    }  
    catch (e) {  
      // this will display an 'error' scene  
      dispatch({  
        type:'Navigate',  
        routeName: 'error',  
        params: {  
          message: e.message || e  
        }  
      });  
    }  
  }  
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过订阅 redux store 的更改来做非常类似的事情。同样，除了作为普通对象的动作之外，这不需要将这个逻辑耦合到导航库。

从外部组件调度场景可能看起来不自然，但它有一个额外的优点，我非常喜欢。因为动作创建者或商店订阅者与导航库是分离的，**对导航逻辑**进行单元测试要简单得多。我有点同意这不一定是一件常见的事情，但另一方面，很少有机会像现在这样用这样的设置来测试它。