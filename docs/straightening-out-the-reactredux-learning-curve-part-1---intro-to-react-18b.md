# 理顺 React/Redux 学习曲线第 1 部分-React 简介

> 原文：<https://dev.to/andrejnaumovski/straightening-out-the-reactredux-learning-curve-part-1---intro-to-react-18b>

免责声明:我将这些文章分成多个部分，这样可以更好地分开，这样我的良心会困扰我，我不得不继续写博客。

*免责声明 2:我将在我写的代码中使用 ES6+语法，因为它更容易阅读，并且是一个现代标准(不使用分号也是我个人的偏好，抱歉)。如果你不习惯 ES6，我在文章后面提供了一个链接，在那里你可以找到很多帮助你学习的资源。*

免责声明 3:我在代码中使用的所有库都可以通过 NPM 获得。我不会在这些系列中涉及 NPM 是如何工作的，所以你应该已经有了基本的知识。

# 一个简介

React 似乎越来越受欢迎。尽管新的 JS 框架层出不穷，但你还是会在每个开发者网站、博客、研讨会上读到 React 和 Redux。所以你决定去看看，看看有什么大惊小怪的。或者，如果你是我，你来自 AngularJS/Angular 4 的背景，你的第一份实际工作是在一个基于 React 的项目中。所以你打开 YouTube/plural sight/其他一些有课程和教程的网站，进入 React 的世界。但是在第一个视频开始 10 分钟后，你已经听说了*动作、减速器、存储、组件、状态、高阶组件*等，你坐在那里想知道“我是不是太笨了，还是这太复杂了？”。

答案，很复杂。**一见钟情**。

因为不一样所以复杂。很有可能，你从未见过这样的东西，尤其是如果你像我一样，有棱角的经验。Angular 中的结构基于控制器(Angular js)/组件(Angular 2/4)和服务。它类似于后端组织结构，后端开发人员对它很熟悉(尤其是如果你像我一样使用过 Spring)。这应该是有意义的，因为 Angular 2 的目标是企业开发。

在我开始之前，我想让你知道，我没有学习，或者我应该说，我没有学习反应仅仅是因为我想，而是因为我不得不。三个月前，我开始了我的第一份工作，那时我还在上大学，在那里我参与了一个基于 React 的项目。我之前唯一的前端 JavaScript 开发经验是 AngularJS，还有(最近的)Angular 2/4。自从我使用 TypeScript 以来，我必须先赶上 ES6+,所以如果你还没有这样做，我推荐你先看看[这个链接](https://github.com/ericdouglas/ES6-Learning)。不管怎样，我写这些系列是因为我还在发展，希望一个初学者的观点可以帮助其他像我一样迷茫的初学者。

我确实忘了说，尽管我被迫学习 React，但我每天都越来越喜欢它！

几个更多的注意事项，我们将进入反应的权利(我保证！):

**1。你必须适应不同的项目结构。**这仅仅是因为 React/Redux 的核心概念在其他任何地方都不存在。
2**。当你不得不为一个特性编辑/创建许多文件时，不要沮丧。**这在开始时可能会很痛苦，但是以后会节省你的时间(和头痛)，尤其是在调试的时候，相信我。
**3。调整心态去反应和 Redux 的工作流程是需要时间的。**我之前说过，React/Redux 的概念不一样。因此，在你能够自然地思考动作和减少动作之前，你需要时间。我花了一个月的时间才有足够的信心去写一个新的组件。
**4。使用 React/Redux 在工作之外做一个实际项目极大地帮助了我。**当你在一个已经建成的项目上工作时，和你必须自己从头开始建立一个项目时，这是不同的。从头开始做有助于你更彻底地理解概念。

# 潜入 React(它是什么，如何工作，有哪些组件)

在本教程中，我将使用基于问答的结构，回答我刚开始学习 React 时遇到的问题。

好了，现在我们离开了，我们可以开始了。所以，第一个问题，什么是反应？

React 本身只是一个 JavaScript 库，用于构建可以保留状态的 UI 组件。

-听起来很酷。什么是组件呢？组件是可以在 UI 上呈现的简单元素。它们由基本 HTML 标签和其他 React 组件构建而成。React 将 UI 分割成多个组件，这样您就可以，1。重复使用它们，2 .以便您可以隔离每个组件的逻辑。

-好吧，我知道什么是组件。“保留状态”是什么意思？
*保留状态简单来说就是 React 组件可以保持它们所包含的元素的状态，比如一个输入字段的当前值。Angular 使用双向数据绑定来实现这一点。然而，在 React 中，在单个组件的上下文中，一切都以其状态存储。对输入字段的更新应该触发状态的更新，这将重新呈现组件并用状态中的新值更新输入字段。*

-嗯，但这不是有点愚蠢吗？所有这些重新渲染肯定会对性能产生影响？
*不，因为 React 的[虚拟 DOM](https://medium.com/@gethylgeorge/how-virtual-dom-and-diffing-works-in-react-6fc805f9f84e) 。在本系列中，我不会深入探讨虚拟 DOM 是如何工作的，但是我提供的链接包含了对它的很好的描述。你可以在这里查看多个 JS 框架/库的性能基准。您可以在自述文件中找到基准测试结果的链接。*

-好吧，我明白了，虚拟 DOM 很快。还有什么我应该知道的吗？
*事实上，有。我可能应该告诉你什么是 JSX，因为你将使用它来编写你的组件。JSX 是一个预处理步骤，允许您使用类似 XML 的语法来编写组件的外观/结构，而不是使用 React 提供的方法。想要更详细的 JSX 指南，并了解使用和不使用它的区别，请查看[的这个](http://buildwithreact.com/tutorial/jsx)。*

-JSX 看起来比实际使用 React.createElement 可读性更好，太棒了！我如何编写我的第一个组件？有多种方法可以编写一个 React 组件。然而，最新的方法是使用扩展 React 的 ES6 类。组件类，它有一个 render()方法。像这样:

```
//MyComponent.jsx
import React from 'react'

export default class MyComponent extends React.Component {
  render() {
    return <h1>Hello from my first component!</h1>
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

*就是这样！我们刚刚创建了一个简单的 React 组件来呈现 h1 标题。注意到那个`<h1></h1>`标签了吗？那是 JSX 在工作。JSX 预处理器会将其编译成 React 的`createElement`方法。你可以在我之前在博客中添加的关于 JSX 的链接中看到不同之处。*

哇，太棒了！但是，等等，我很困惑，我如何渲染这个组件？或者更好的是，我该如何启动我的应用程序？我们就要到了。为了呈现组件并启动应用程序，您需要一个应用程序的起始点，比如 index.js 文件。但是为了运行 ES6+,你需要巴贝尔等。光是写这些配置和样板代码就让我头疼。幸运的是，有一个工具可以为你生成所有这些。提示[创建-反应-应用](https://github.com/facebookincubator/create-react-app)。出于简单的原因，我假设我们已经用 create-react-app 设置了应用程序(它有一个非常棒的文档)，它应该在 **src** 目录:
中生成这些文件

```
App.css
App.js
App.test.js
index.css
index.js
logo.svg
registerServiceWorker.js 
```

Enter fullscreen mode Exit fullscreen mode

index.js 是我们应用程序的起点。它将我们的 index.html 文件中 id =“root”的元素替换为已编译的 App 组件，该组件当前呈现自动生成的 UI。`ReactDOM.render()`方法可以有且只有一个父标签，这就是为什么我们将在 App 组件中呈现所有组件(以及将来的路线)。首先，将您的 MyComponent.jsx 文件添加到与上述文件相同的目录中。然后，打开 App.js，修改如下:T3】

```
import React, { Component } from 'react'
import MyComponent from './MyComponent'
import './App.css'

class App extends Component {
  render() {
    return (
      <div className="App">
        <MyComponent />
      </div>
    )
  }
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

*我们在这里做的是`import`我们从`MyComponent`导出的类，然后将它添加为主 div 标签的子标签。*

这将把我们的`MyComponent`组件呈现为`App`组件的一部分，而不是之前生成的内容。React 组件可以将其他 React 组件作为子组件，这些子组件又可以将其他 React 组件作为子组件等。对如何构建组件没有限制，这意味着您可以在任何您喜欢的地方重用您的组件，这就是 React 的伟大之处。

我们希望有一个组件来呈现其他组件，因为正如我提到的，我们在 index.js 中使用的 render 方法只能有一个 parent 标记。

# 第一部分结论

首先，我认为这应该足够了。我相信我已经涵盖了初学者在第一次进入 React 时开始问的大多数问题，然后给出了一个简单的组件示例。

*等等，你之前说的组件状态我没看到啊！*

这正是我们将在第 2 部分中做的。我们将在 React 和传递道具给孩子中讨论组件状态。拜托，我刚刚开始写博客，所以如果你对我有任何建议，请在评论区告诉我，我将不胜感激。请继续关注第 2 部分！