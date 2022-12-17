# 用 React React 创建一个简单且经过测试的 Redux 类应用程序

> 原文：<https://dev.to/scastiel/create-a-simple-and-tested-redux-like-app-with-reason-react>

在过去的几周里，我成了[理性](https://reasonml.github.io/)的忠实粉丝，尤其是它与[理性反应](https://reasonml.github.io/reason-react/)的关联。因为 Reason 和 React 都是非常年轻的项目，所以还没有很多关于它的教程、StackOverflow 问题和文档。所以开始一个新项目并不像官方网站想的那么简单。

但是，使用纯函数式语言来创建 React 应用程序不仅是可能的，而且也是非常令人兴奋的。让我们看看我们如何能做它。

当然，我看到了使用 Reason 进行前端开发的许多优势，但这不是我在本文中想要谈论的。如果你正在读它，你可能已经被说服了(如果不是，那不是问题！).我想写的是更实用的教程，希望它能防止一些人花费数小时寻找我必须找到的相同答案。

该教程基于我创建的一个非常小(并且无用)的项目，由一个带有按钮的计数器组成，可以增加或减少它(我告诉过你它是无用的)。想法是创建一个 React 应用程序，类似 Redux 架构(有状态、动作和缩减器)，以及相关的单元测试。

还要知道，如果你已经掌握了一些关于 Reason 语法、类似 Redux 的体系结构(我们在这里将保持它非常简单)以及 React 的知识，那么理解本教程将会更加容易。这里有两篇文章将向你介绍理性和理性反应:

*   [一个由](https://dev.to/kayis/a-reason-to-code) [K 编码](https://twitter.com/K4y1s)的理由👓
*   [一个理性的反应教程](https://jaredforsyth.com/2017/07/05/a-reason-react-tutorial/)作者[杰瑞德·福赛思](https://twitter.com/jaredforsyth)

现在让我们开始吧！完整的项目可以在 [GitHub](https://github.com/scastiel/reason-react-example-app) 上找到。在开始之前，您可能需要了解以下信息:

*   这个项目是由棒极了的 [Create React App](https://github.com/facebookincubator/create-react-app) 启动的，使用了 [Reason 脚本](https://github.com/reasonml-community/reason-scripts)，由[React](https://reasonml.github.io/reason-react/gettingStarted.html#getting-started-reason-scripts)推荐。
*   我没有使用现有的类似 Redux 的库 [Reductive](https://github.com/reasonml-community/reductive) 来管理应用程序的状态。它可能符合我的需要，但它仍然非常年轻，缺乏文档。也许如果它长大了，在不久的将来它会对使用它感兴趣。
*   测试是用 JavaScript 编写的。虽然有可能合理地编写它们，但是 [bs-jest](https://github.com/reasonml-community/bs-jest) 仍然是非常“实验性和正在进行中的工作”(正如他们自己所说的)，并且我无法实现一些事情，比如使用模拟函数。它们似乎已经实现，但是没有任何示例文档。同样，在将来直接用 rational 编写所有的测试将会很有趣。

## 描述我们的应用程序的状态

状态基本上是一种类型，对应于我们希望存储在应用程序状态中的数据。如果我们只想存储一个整数，我们可以定义:

```
type state = int; 
```

Enter fullscreen mode Exit fullscreen mode

在我们的示例应用程序中，我们希望存储由两个字段组成的记录:

```
type state = {
  counter: int,
  intervalId: option intervalId
}; 
```

Enter fullscreen mode Exit fullscreen mode

注意类型名`state`很重要，我们稍后会看到原因。

在我们的 *state.re* 文件中，我们还声明了一些实用函数来创建和操作状态。实际上，它们主要是帮助编写我们的 JavaScript 测试，因为在 JavaScript 中，我们不知道记录是如何存储的。

所以我们不可能写出这样的东西:

```
const state = { counter: 0, intervalId: 123 } 
```

Enter fullscreen mode Exit fullscreen mode

...我们会写:

```
const state = setCounter(createState(), 0) 
```

Enter fullscreen mode Exit fullscreen mode

## 定义可能的动作

### 动作定义

动作由类型和参数组成。例如，如果我们想将某个状态值设置为 10，我们可以有一个带有类型`SetValue`和一个参数`10`的动作。理智的[变种](https://reasonml.github.io/guide/language/variant)型正是我们所需要的；我们可以在一个变量类型中定义所有可能的动作:

```
type action =
  | Increment
  | Decrement
  | StartIncrementing intervalId
  | StopIncrementing; 
```

Enter fullscreen mode Exit fullscreen mode

同样，为了使 JavaScript 测试更容易，我们还定义了一些实用函数和值:

```
let incrementAction = Increment;
let decrementAction = Decrement;
let startIncrementingAction intervalId => StartIncrementing intervalId;
let stopIncrementingAction = StopIncrementing; 
```

Enter fullscreen mode Exit fullscreen mode

这将有助于创建新的动作(我们无法访问 JavaScript 中的 variant 类型构造函数)，也有助于将一些结果动作与我们预期的动作进行比较。

### 动作创建者

在我们的应用程序中，不使用动作构造函数，用实用函数创建动作更容易。例如，要创建一个`Increment`动作，我们可以使用函数`increment` :

```
let increment => Increment;
let setValue value => SetValue value;

let incrementAction = increment;
let setValueTo10Action = setValue 10; 
```

Enter fullscreen mode Exit fullscreen mode

现在这看起来不是很有用，但是让我们想象我们经常想要增加我们的计数器两次。我们想写一个动作创建器来触发两个动作。为此，我们定义我们的动作创建者将把函数`dispatch`作为最后一个参数，该函数将被调用来触发动作:

```
let increment dispatch => dispatch Increment;

let incrementTwice dispatch => {
  dispatch Increment;
  dispatch Increment;
} 
```

Enter fullscreen mode Exit fullscreen mode

此外，我们现在可以编写异步动作创建器(有副作用)，比如 HTTP 请求、超时等。:

```
let incrementEverySecond dispatch => {
  let intervalId = setInterval (fun () => increment dispatch) 1000;
  startIncrementing intervalId dispatch
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们将在后面看到如何调用这些动作创建者，但是请注意，我们定义了一个类型`deferredAction`(这将有助于我们进行类型推断)，对应于在没有`dispatch`参数的情况下调用动作创建者时返回的内容:

```
type deferredAction = (action => unit) => unit;

/* For instance `deferredAction` is the type of `increment`. */ 
```

Enter fullscreen mode Exit fullscreen mode

## 编写减速器

reducer 是一个函数，它接受两个参数:当前状态和一个动作，并返回从动作计算出的新状态。同样为了使类型推理更容易，我们定义了一个类型:

```
open State;
open Actions;
type reducer = state => action => state; 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们在动作类型
上使用模式匹配来定义我们的`reducer`函数

```
let reducer: reducer =
  fun state action =>
    switch action {
    | Increment => {...state, counter: state.counter + 1}
    | StartIncrementing intervalId =>
      switch state.intervalId {
      | None => {...state, intervalId: Some intervalId}
      | _ => state
      }
    }; 
```

Enter fullscreen mode Exit fullscreen mode

## 设计 React 组件

我们的示例应用程序由一个名为`Counter`的主要 React 组件组成。我们希望它是完全无状态的，所以我们需要给它参数(道具)*状态*(我们希望显示或使用什么值)和*动作*，作为一些事件(点击按钮)调用的函数。

下面是该组件的简化版本:

```
let component = ReasonReact.statelessComponent "Counter";

let make
    counter::(counter: int)
    increment::(increment: unit => unit)
    _children => {
  ...component,
  render: fun self =>
    <div>
      (ReasonReact.stringToElement ("Counter: " ^ string_of_int counter))
      <button className="plus-button" onClick=(self.handle (fun _ _ => increment ()))>
        (ReasonReact.stringToElement "+")
      </button>
    </div>
}; 
```

Enter fullscreen mode Exit fullscreen mode

注意`increment` prop 的类型:它是一个不返回任何东西的函数(`unit`)。我们不知道我们以前创建的动作，我们只是有一个函数，我们必须在需要时调用它，它有一个 React React 需要的奇怪语法:`self.handle (fun _ _ => increment ())`。想象一下它将如何使单元测试变得更容易！

## 连接所有的棋子

现在我们已经有了状态定义、它们的创建者的操作、我们的 reducer 和一个组件来显示和操作所有这些部分，我们需要组装所有这些。

先从 app 的主文件 *index.re* 说起。它首先定义了一个函数`createComponent` :

```
let createComponent state dispatch => <CounterApp state dispatch />; 
```

Enter fullscreen mode Exit fullscreen mode

该函数将状态作为第一个参数，将函数`dispatch`作为第二个参数。它返回一个名为`CounterApp`的组件的新实例，我们将在几分钟后看到，给它两个参数`state`和`dispatch`。

我们把这个函数作为参数给另一个组件，`Provider` :

```
ReactDOMRe.renderToElementWithId
  <Provider reducer initialState=(createState ()) createComponent /> "root"; 
```

Enter fullscreen mode Exit fullscreen mode

这个`Provider`组件将处理我们应用程序的生命周期。无需深入细节(参见模块 *providerFactory* 了解更多)，它创建一个具有状态(应用程序的当前状态)的组件，并在发出动作时使用 reducer 更新该状态。它基本上是 *redux-react* 所做的事情的重新实现，以一种更加简单和极简的方式。

还要注意，*提供者*组件是通过调用模块 *ProviderFactory 创建的。MakeProvider* 带有作为参数的另一个模块:`State`，它包含我们状态的类型:`state`。这就是为什么我们的国家类型需要被称为`state`； *ProviderFactory* 模块不知道我们的状态，它甚至可以在一个单独的项目中，所以使它关于状态类型是通用的是有用的，因为有了`createComponent`参数，封装的组件也是如此。

最后，我们需要`CounterApp`组件，它将是提供者和`Counter`组件之间的链接。它的两个道具是应用程序的当前状态，以及一个将被调用来发出动作的`dispatch`函数:

```
let component = ReasonReact.statelessComponent "CounterApp";

let make state::(state: state) dispatch::(dispatch: deferredAction => unit) _children => {
  ...component,
  render: fun _ => {
    let onIncrement () => dispatch increment;
    <Counter
      counter=state.counter
      increment=onIncrement
    />
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

因为`Counter`需要一个普通函数(`unit => unit`)作为`increment`参数，我们通过调用`dispatch` :
来创建它

```
let onIncrement () => dispatch increment; 
```

Enter fullscreen mode Exit fullscreen mode

## 编写单元测试

既然我们的应用程序正在运行，我们可以考虑如何为每个部分编写单元测试了。如果您习惯于为 React 组件编写测试，那么进行转换应该不会太难。关于在普通 JavaScript 中使用 Reason 的东西(组件、函数……)，有一些事情需要了解。

### 减速器

测试减压器是最简单的部分:这是一个纯粹的函数，我们只需要测试给定一个状态和一个动作，我们得到预期的新状态。

例如，下面是如何测试`Increment`动作的:

```
describe('with Increment action', () => {
  it('increments counter', () => {
    const state = setCounter(createState(), 0)
    const newState = reducer(state, incrementAction)
    expect(newState).toEqual(setCounter(state, 1))
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们使用我们的效用函数`setCounter`和`setState`，因为我们不能(至少不能以一种清晰的方式)从零开始创建一个状态(参见关于状态定义的部分)。

### 动作创建者

只要没有诸如超时、HTTP 请求等副作用，测试动作创建者并不困难。

例如，为了测试`increment`动作创建器，我们需要测试当用`dispatch`函数(一个玩笑间谍)调用时，这个`dispatch`函数将被用`Increment`动作:
调用

```
describe('increment', () => {
  it('should call dispatch with Increment action', () => {
    const dispatch = jest.fn()
    increment(dispatch)
    expect(dispatch.mock.calls.length).toEqual(1)
    expect(dispatch.mock.calls[0][0]).toEqual(incrementAction)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

再次注意，我们必须使用我们的效用值`incrementAction`来检查结果值是否是一个`Increment`动作，因为我们不确定这个变量类型在 JavaScript 中是如何转换的。

如果被测试的动作创建器是异步的，过程是完全一样的，我们将使用 Jest 能力来测试带有`async`函数的异步代码(参见 *action.test.js* 文件中的一些例子)。

### 组件

测试组件真的很容易，只需要知道一件事:React 组件不能在 JavaScript 中使用的原因。要在 JavaScript 中使用 React 组件，必须导出该组件的 JS-friendly 版本。例如在*文件的末尾有一个*文件:

```
let counter =
  ReasonReact.wrapReasonForJs
    ::component
    (
      fun jsProps =>
        make
          counter::jsProps##counter
          increment::jsProps##increment
          [||]
    ); 
```

Enter fullscreen mode Exit fullscreen mode

现在，在测试文件(或任何 JavaScript 文件)中，我们可以导入我们的组件，并将其用作任何 React 组件:

```
import { counter as Counter } from '../counter.re' 
```

Enter fullscreen mode Exit fullscreen mode

测试部分现在仍然和测试任何 React 组件一样，没有什么特别的技巧可以使用。为了证明这一点，下面是我如何测试我的`Counter`组件:

#### 用快照测试渲染

在给定某些属性的情况下，测试组件渲染良好的最简单方法是使用快照。例如，如果我们想检查计数器的呈现元素在计数器为 0 或 10 时是否正常，我们写:

```
import { shallow } from 'enzyme'
describe('Counter component', () => {
  it('renders with value 0 without intervalId', () => {
    const wrapper = shallow(<Counter counter={0} />)
    expect(wrapper).toMatchSnapshot()
  })

  it('renders with value 10 without intervalId', () => {
    const wrapper = shallow(<Counter counter={10} />)
    expect(wrapper).toMatchSnapshot()
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

第一次启动时，Jest 将生成快照文件，下一次它将比较呈现的元素是否仍然相同。

#### 测试动作

为了测试当一个按钮被点击时，正确的函数将被调用，我们将使用 *enzyme* 能力来模拟点击和 Jest mock 函数。这很容易:

```
it('calls increment when plus button is clicked', () => {
  const increment = jest.fn()
  const wrapper = shallow(
    <Counter counter={10} increment={increment} />
  )
  wrapper.find('.plus-button').simulate('click')
  expect(increment.mock.calls.length).toEqual(1)
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 接下来是什么？

好了，现在我们知道了如何合理地创建一个简单的 React 组件，使用类似 Redux 的架构和单元测试。如果我们看一下 React/Redux 能做什么，我们可以想象接下来要实现很多:

*   我们的应用程序的路由器，基于它的当前状态。甚至可能将状态存储在本地存储中？
*   编排几个更复杂的组件，几个减速器…
*   使用 React Native！那将是惊人的；我听说有些人已经成功做到了😉

Reason 还是一门很年轻的语言，它的生态系统成长很快，很牛逼。我已经不得不重写本教程的一些部分，因为自从我开始以来出现了新的功能或项目。毫无疑问，它将继续下去😃

*这篇文章最初发表在我的博客上。*