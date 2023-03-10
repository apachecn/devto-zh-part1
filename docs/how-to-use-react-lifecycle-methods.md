# 如何使用 React 生命周期方法

> 原文：<https://dev.to/a_reiterer/how-to-use-react-lifecycle-methods>

如果您不知道哪种方法适用于您的特定用例，React 生命周期方法可能会令人困惑。今天我将向您展示存在哪些生命周期方法，以及如何正确使用它们。

## 简介

React 组件有几个“生命周期方法”，允许我们在特定时间执行动作(例如:从服务器获取数据)。当我开始学习 React 时，我发现很难弄清楚我应该对某些行为使用哪种生命周期方法。如果你也是这种情况，这篇文章应该是一个方便的指南。

我将从所有生命周期方法的概述开始，并解释它们被调用的顺序。然后我会用简短的解释和一些用例来处理它们。最后，您应该更好地了解何时使用哪种生命周期方法。

## React 组件的生命周期

让我们根据 React 文档从组件的生命周期开始。组件生命周期中有三个特定的阶段，它们对我们的生命周期方法非常重要，我将对此进行解释:

*   增加
*   更新
*   卸载

### 挂载

当 React 创建一个组件的实例并将其插入 DOM 时( *mounting* )，调用以下方法:

*   *构造函数()*
*   *componentWillMount()*
*   *render()*
*   *componentidmount()*

### 更新

如果组件的属性或状态由于任何原因而改变，则执行组件的更新。但是，这意味着组件必须重新呈现，这将导致调用以下方法:

*   *组件别墅接收插件()*
*   *shouldcomponentdupdate()*
*   *componentWillUpdate()*
*   *render()*
*   *componentDidUpdate()*

### 卸载

在某个时候，我们的组件将再次从 DOM 中移除。该过程称为*卸载*，意味着调用以下方法:

*   *componentWillUnmount*

### React 组件生命周期摘要

我希望我能给你一个 React 组件生命周期和生命周期方法调用顺序的简短概述。为了简明起见，这里按正确的顺序列出了所有的生命周期方法。

*   *componentWillMount*
*   *组件安装*
*   *组件别墅接收插件*
*   *shouldComponentUpdate*
*   *组件将更新*
*   *componentiddupdate*
*   *componentWillUnmount*

你可以看到，他们不是很多。然而，为不同的用例选择正确的方法以防止副作用或错误是很重要的。

## 生命周期方法

在本节中，我们将探索不同的生命周期方法。我将详细解释它们中的每一个，并尽我所能提供不同的用例示例，以便更好地理解。

### componentWillMount()

```
componentWillMount() 
```

Enter fullscreen mode Exit fullscreen mode

每当 React 渲染一个组件时，它都会调用 c _ omponentWillMount _ first。注意，这个方法在一个组件的生命周期中只被调用一次，而且是在它被初始化之前。因此，没有对 DOM 的访问。

**注意:**因为 componentWillMount 是在 *render()* 方法之前被调用的，所以当你使用服务器端渲染时，这是唯一一个在服务器端被调用的生命周期方法。

除了这个生命周期挂钩，React 文档建议使用构造函数。

#### 状态

可以在这个方法内部使用 *this.setState(…)* 。但是要注意，当你同步设置状态**时， [**可能不会触发重新渲染**](https://facebook.github.io/react/docs/react-component.html#componentwillmount) 。**

如果可以，我建议在构造函数内部设置默认状态，而不是在这里设置状态。

#### 用例

我没有找到很多 componentWillMount 的用例。有些人建议用它来做一些根组件的配置，这些配置只能在运行时完成(例如:[建立一个 Firebase 连接](https://engineering.musefind.com/react-lifecycle-methods-how-and-when-to-use-them-2111a1b692b1)

### 组件装载

```
componentDidMount() 
```

Enter fullscreen mode Exit fullscreen mode

无论何时调用这个方法，React 都已经呈现了我们的组件并将其放入 DOM。因此，如果您想执行任何依赖于 DOM 的初始化，现在就做吧。

#### 状态

可以用 *this.setState()* 设置状态。每当您这样做时，它还会触发组件的重新渲染。

#### 用例

您可以使用 componentDidMount 通过 AJAX 调用从服务器获取数据。另外，如果你需要**初始化任何依赖于 DOM 的东西，**你可以在这里完成(比如初始化像 D3 这样的第三方库)。最后但同样重要的是，您可以**在 componentDidMount 中添加事件监听器**。

### [组件别墅接收插件](#componentwillreceiveprops)

```
componentWillReceiveProps(nextProps) 
```

Enter fullscreen mode Exit fullscreen mode

每当一个组件接收到一组新的道具时，这个方法将被首先调用。另外，请注意，React 调用这个方法，即使在属性没有改变的情况下。因此，无论何时使用这种方法，一定要将 *this.props* 与 *nextProps* 进行比较，以避免设置不必要的状态。

React 不会在 **mount** 进程中调用这个方法。相反，如果组件的一些属性可能会更新，它只调用这个方法。

#### 状态

您可以使用 *this.setState()* 来设置状态

#### 用例

如果你有一个状态，它是由多个道具计算出来的，你可以在这里进行计算。别忘了检查你的相关道具是否真的有变化(对比 *this.props* 和 *nextProps*

### shouldcomponentdupdate

```
shouldComponentUpdate(nextState, nextProps) 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，不会实现该方法，因此每次更新状态或属性都会导致渲染，即使属性没有改变。然而，如果你想避免不必要的渲染，你可以在这里处理。返回 *false* 表示 React 不会执行 *componentWillUpdate()* 、 *render()* 和 *componentDidUpdate()* 。

初始呈现时不调用此方法。

**注意:**根据 React 文档，React 可能会将 shouldComponentUpdate 视为一个提示，而不是严格遵循它的返回值。这意味着，该方法可能返回 *false* ，但 React 仍然决定重新呈现组件。

#### 状态

这里不能调用 *setState* 。同样，这样做也没有多大意义。如果因为改变道具而要设置状态，则使用*componentWillReceiveProps*代替。

#### 用例

如前所述，您可以检查属性或状态的更新是否真的会影响组件的输出。为此，您可以将当前道具/状态与下一个道具/状态进行比较。如果组件不应该更新，只要返回 false，组件就不会更新。

注意:这可能会导致严重的副作用。React 也为这个用例提供了另一个解决方案:如果你注意到某个组件很慢，你可以从 *React 继承它。PureComponent* 代替*做出反应。组件*。它将对道具和状态进行简单的比较，这可能适用于我现在能想象的大多数用例。

### 组件更新

```
componentWillUpdate(nextProps, nextState) 
```

Enter fullscreen mode Exit fullscreen mode

这个方法在渲染之前被调用。和*shouldcomponentdupdate*一样，每当有新的 props 传递给组件，或者状态改变时，都会调用它。

初始呈现时不调用此方法。

#### 状态

这里不能调用 *setState* 。同样，如果你想因为改变道具而设置状态，使用*componentWillReceiveProps*代替。

#### 用例

您可以在更新组件之前执行必要的准备工作。这个生命周期方法在 render()之前被调用，所以你不应该做任何依赖于 DOM 的事情——它很快就会过时。

常见的使用案例似乎是:

*   基于状态变化设置变量
*   调度事件
*   [开始动画制作](https://stackoverflow.com/questions/33075063/what-is-the-exact-usage-of-componentwillupdate-in-reactjs/33075514#33075514)

### componentDidUpdate

```
componentDidUpdate(prevProps, prevState) 
```

Enter fullscreen mode Exit fullscreen mode

耶！一切顺利，React 更新了我们的组件。渲染之后，React 还会直接调用 *componentDidUpdate* 。

初始呈现时不调用此方法。

#### 状态

你可以在这里使用 *setState* 。

#### 用例

如果在组件更新之后，您必须对 DOM 做一些事情，那么此时此地正是时候。一个很好的例子就是更新第三方 UI 库，比如 D3，来传递新数据。

这里也是**执行网络请求**的好地方，只要你将当前状态/道具与之前的状态/道具进行对比，避免不必要的网络请求。

### componentWillUnmount

```
componentWillUnmount() 
```

Enter fullscreen mode Exit fullscreen mode

就在 React 卸载并销毁我们的组件之前，它调用了 *componentWillUnmount* 。

#### 状态

在卸载组件之前，不能设置状态。

#### 用例

使用此挂钩执行清理操作。这可能是

*   移除您在 *componentDidMount* (或其他地方)中添加的事件监听器
*   取消活动的网络请求
*   无效计时器
*   清理您在*componentid mount*中创建的 DOM 元素

## 包装完毕

今天，您已经了解了 React 组件的生命周期由三个阶段组成:安装、更新和卸载。

您还学习了 React 在每个阶段调用一组特定的生命周期方法。您可以根据您想要实现的用例来使用它们。

谢谢你阅读这篇文章。我真的希望你喜欢它。另外，如果你能和你的朋友分享这篇文章，我将非常感激。

如果你有什么想补充的，或者你只是想聊聊关于开发的事情，帮我联系上 [Twitter](https://www.twitter.com/a_reiterer) ，或者发邮件到 [hi@andreasreiterer.at](//mailto:hi@andreasreiterer.at) 。

# 行动号召

你也想成为更好的开发者？我在我的博客上分享我所知道的，如果你订阅了我的[每周简讯](//www.andreasreiterer.at/weekly-webdev-newsletter/)，我会将更多关于 React 的技巧和窍门以及其他 web 开发文章发送到你的收件箱中。

*帖子[如何使用 React 生命周期方法](http://www.andreasreiterer.at/web-development/reactjs-lifecycle-methods/)首先出现在[我的博客](http://www.andreasreiterer.at)上。*