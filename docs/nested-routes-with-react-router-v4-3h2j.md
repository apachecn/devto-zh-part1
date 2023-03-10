# 使用 React 路由器 v4 的嵌套路由

> 原文：<https://dev.to/tylermcginnis/nested-routes-with-react-router-v4-3h2j>

* * *

这篇文章最初发表在 TylerMcGinnis.com 大学，是他们 T2 反应路由器课程的一部分。

* * *

React Router v4 引入了一种新的声明式、基于组件的路由方法。这种方法给嵌套路由带来了一些混乱。在本帖中，我们将打破这种混乱，您将学习如何使用 React 路由器嵌套路由。

### 视频

[https://www.youtube.com/embed/sfvrjwVihFY](https://www.youtube.com/embed/sfvrjwVihFY)

### 岗位

为了理解递归，首先需要熟悉函数、返回值和调用栈。类似地，为了理解嵌套路由，您首先需要熟悉 React 路由器最基本的组件`<Route>`。

`<Route>`接收一个`path`和一个`component`。当您的应用程序的当前位置与`path`匹配时，`component`将被渲染。当它不存在时，`Route`会呈现 null。

我喜欢这样想，每当你使用`<Route path='/path' component={X} />`，它总是会渲染一些东西。如果应用程序的位置与`path`匹配，它将渲染`component`。如果没有，就会渲染`null`。

> 我知道我们在这里开始得很慢，但如果你完全掌握了最后一段，本教程的其余部分将会很容易🍰。

现在，让我们来看看我们将要构建的例子。这个想法是我们有一个主题列表，这些主题有资源，这些资源有一个 url。这是我们正在使用的数据结构。

```
const topics = [
  {
    name: 'React Router',
    id: 'react-router',
    description: 'Declarative, component based routing for React',
    resources: [
      {
        name: 'URL Parameters',
        id: 'url-parameters',
        description: "URL parameters are parameters whose values are set dynamically in a page's URL. This allows a route to render the same component while passing that component the dynamic portion of the URL so it can change based off of it.",
        url: 'https://tylermcginnis.com/react-router-url-parameters'
      },
      {
        name: 'Programatically navigate',
        id: 'programmatically-navigate',
        description: "When building an app with React Router, eventually you'll run into the question of navigating programmatically. The goal of this post is to break down the correct approaches to programmatically navigating with React Router.",
        url: 'https://tylermcginnis.com/react-router-programmatically-navigate/'
      }
    ]
  },
  {
    name: 'React.js',
    id: 'reactjs',
    description: 'A JavaScript library for building user interfaces',
    resources: [
      {
        name: 'React Lifecycle Events',
        id: 'react-lifecycle',
        description: "React Lifecycle events allow you to tie into specific phases of a component's life cycle",
        url: 'https://tylermcginnis.com/an-introduction-to-life-cycle-events-in-react-js/'
      },
      {
        name: 'React AHA Moments',
        id: 'react-aha',
        description: "A collection of 'Aha' moments while learning React.",
        url: 'https://tylermcginnis.com/react-aha-moments/'
      }
    ]
  },
  {
    name: 'Functional Programming',
    id: 'functional-programming',
    description: 'In computer science, functional programming is a programming paradigm—a style of building the structure and elements of computer programs—that treats computation as the evaluation of mathematical functions and avoids changing-state and mutable data.',
    resources: [
      {
        name: 'Imperative vs Declarative programming',
        id: 'imperative-declarative',
        description: 'A guide to understanding the difference between Imperative and Declarative programming.',
        url: 'https://tylermcginnis.com/imperative-vs-declarative-programming/'
      },
      {
        name: 'Building User Interfaces with Pure Functions and Function Composition',
        id: 'fn-composition',
        description: 'A guide to building UI with pure functions and function composition in React',
        url: 'https://tylermcginnis.com/building-user-interfaces-with-pure-functions-and-function-composition-in-react-js/'
      }
    ]
  }
] 
```

这个模式将很好地映射到嵌套的路线，我们的应用程序最终将看起来像这样

[![nested routes react router](img/2f6f0dc7d4d0b59d6cad41a35f58ae90.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QriDFDN0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/./nested-example.png)

> 我鼓励你在这里使用完整的例子。

在我们开始担心嵌套路线之前，让我们首先创建我们的应用程序的框架，包括导航栏，它将允许我们在主页(`/`)和主题(`/topics`)之间导航。

```
import React, { Component } from 'react'
import {
  BrowserRouter as Router,
  Link,
  Route // for later
} from 'react-router-dom'

class App extends Component {
  render() {
    return (
      <Router>
        <div style={{width: 1000, margin: '0 auto'}}>
          <ul>
            <li><Link to='/'>Home</Link></li>
            <li><Link to='/topics'>Topics</Link></li>
          </ul>
        </div>
      </Router>
    )
  }
}

export default App 
```

这是一个坚实的开端。现在我们要做的是渲染几个`<Route>`以便 UI 会根据我们当前所处的路径而改变。然而，在我们这样做之前，我们需要继续构建组件，如果有匹配，我们将把这些组件传递给我们的`<Route>`来呈现。我们称它们为`Home`和`Topics`。现在，当我们把事情设置好了，让它们都呈现一个标题，上面写着`HOME`或者`TOPICS`。

```
function Home () {
  return (
    <h1>
      HOME
    </h1>
  )
}

function Topics () {
  return (
    <h1>
      TOPICS
    </h1>
  )
} 
```

完美。现在，我们将使用`<Route>`组件告诉 React Router，无论何时有人在`/`上，它都应该呈现`Home`组件，无论何时有人在`/topics`上，它都应该呈现`Topics`组件。

```
class App extends Component {
  render() {
    return (
      <Router>
        <div style={{width: 1000, margin: '0 auto'}}>
          <ul>
            <li><Link to='/'>Home</Link></li>
            <li><Link to='/topics'>Topics</Link></li>
          </ul> 
          <hr />

          <Route exact path='/' component={Home} />
          <Route path='/topics' component={Topics} />
        </div>
      </Router>
    )
  }
} 
```

在这一点上，我们有一个我们之前讨论过的非常简单的例子，“`<Route>`接受一个`path`和一个`component`。当您的应用程序的当前位置与`path`匹配时，`component`将被渲染。当它不存在时，`Route`将呈现 null。

如果我们在`/`，我们会看到导航条和`Home`组件。如果我们在`/topics`，我们会看到导航条和`Topics`组件。

最后，我们有了一个足够好的开端，可以开始讨论如何用 React 路由器处理嵌套路由。谢谢你的耐心🤝。

如果你回头看看[的最后一个例子](https://csb-v8k006wnol-xdwmyvgvdg.now.sh/topics/reactjs/react-lifecycle)，你会注意到当我们转到`/topics`时，我们得到的 UI 是另一个导航条。让我们修改我们的`Topics`组件来做到这一点。这次不是硬编码我们的`Link`,而是需要使用我们的`topics`数组为每个高级主题创建一个`Link`。

```
function Topics () {
  return (
    <div>
      <h1>Topics</h1>
      <ul>
        {topics.map(({ name, id }) => (
          <li key={id}>
            <Link to={`/topics/${id}`}>{name}</Link>
          </li>
        ))}
      </ul>
    </div>
  )
} 
```

现在，当我们转到`/topics`并且渲染`Topics`组件时，我们将得到三个`Link`,每个代表一个高级主题。注意我们链接到哪里，`/topics/${id}`。如果我们要将某人链接到`/topics/${id}`，这意味着我们需要渲染一个`Route`来匹配该路径。这是 React Router 中嵌套路由的第一个重要概念——不管您是在主组件中还是在嵌套组件中渲染一个`Route`,如果`path`匹配，就会渲染`component`。考虑到这一点，如果我们在我们的`Topics`组件中呈现一个`Route`并让它与`/topics/:topicId`匹配会怎么样？

```
function Topic () {
  return (
    <div>
      TOPIC
    </div>
  )
}

function Topics () {
  return (
    <div>
      <h1>Topics</h1>
      <ul>
        {topics.map(({ name, id }) => (
          <li key={id}>
            <Link to={`/topics/${id}`}>{name}</Link>
          </li>
        ))}
      </ul> 
      <hr />

      <Route path={`/topics/:topicId`} component={Topic}/>
    </div>
  )
} 
```

🤯🤯🤯🤯🤯🤯

这就是理解`Route`如此重要的原因。从早些时候到现在什么都没有改变，但是由于某种原因，你的大脑变得兴奋起来，因为我们在主要的`App`组件之外渲染了一个`Route`。

当我们转到`/topics`时，会呈现出`Topic`组件。`Topics`然后渲染一个导航条和一个新的`Route`，它将匹配我们刚刚渲染的导航条中的任何`Link`(因为`Link`链接到`/topics/${id}`而`Route`匹配`/topics/:topicId`)。这意味着如果我们点击`Topics`组件中的任何一个`Link`，那么`Topic`组件将会被渲染，它现在只显示单词`Topic`。

> 需要注意的是，仅仅因为我们匹配了另一个`Route`组件，并不意味着之前匹配的`Route`组件不再被渲染。这是很多人困惑的地方。记住，把`Route`想象成渲染另一个组件或者 null。在 React 中嵌套普通组件的方式可以直接应用于嵌套`Route` s。

在这一点上，我们进展顺利。如果由于某种原因，你的团队中另一个不熟悉 React 路由器的成员决定将`/topics`改为`/concepts`该怎么办？他们可能会前往主要的`App`组件，并改变`Route`

```
// <Route path='/topics' component={Topics} />
<Route path='/concepts' component={Topics} /> 
```

NBD。问题是，这完全破坏了应用程序。在`Topics`组件内部，我们假设路径以`/topics`开始，但是现在它被改成了`/concepts`。我们需要的是让`Topics`组件接收任何初始路径作为道具的方法。这样，不管某人是否改变了父代`Route`，它都会一直工作。对我们来说，好消息是 React 路由器确实做到了这一点。每次使用 React Router 渲染一个组件时，该组件都会被传递三个道具- `location`、`match`和`history`。我们关心的是`match`。`match`将包含关于`Route`如何匹配的信息(正是我们需要的)。具体来说，它有两个我们需要的属性，`path`和`url`。这些非常相似，医生是这样描述的-

`path - The path pattern used to match. Useful for building nested <Route>s`

`url - The matched portion of the URL. Useful for building nested <Link>s`

这些定义中有一个重要的观点。用`match.path`建造嵌套的`Route`，用`match.url`建造嵌套的`Link`

回答“为什么”的最好方法是看一个例子。

> 如果您不熟悉 URL 参数，请在继续之前阅读 [React Router v4: URL 参数](https://tylermcginnis.com/react-router-url-parameters)。

假设我们正在使用一个嵌套了 route 的应用程序，并且当前的 URL 是`/topics/react-router/url-parameters`。

如果我们将`match.path`和`match.url`记录在嵌套最深的组件中，我们会得到如下结果。

```
render() {
  const { match } = this.props // coming from React Router.

  console.log(match.path) // /topics/:topicId/:subId

  console.log(match.url) // /topics/react-router/url-parameters

  return ...
} 
```

请注意，`path`包含了 URL 参数，而`url`只是完整的 URL。这就是为什么一个用于`Link` s，另一个用于`Route` s

当你创建一个嵌套链接时，你不希望使用 URL 参数。你想让用户直接去`/topics/react-router/url-parameters`。这就是为什么`match.url`更适合嵌套的`Link`的原因。然而，当您将某些模式与`Route`匹配时，您希望包含 URL 参数——这就是为什么`match.path`用于嵌套的`Route`

让我们回到我们的例子，现在，我们正在将`/topics`硬编码到我们的`Route`和`Link`中

```
function Topics () {
  return (
    <div>
      <h1>Topics</h1>
      <ul>
        {topics.map(({ name, id }) => (
          <li key={id}>
            <Link to={`/topics/${id}`}>{name}</Link>
          </li>
        ))}
      </ul> 
      <hr />

      <Route path={`/topics/:topicId`} component={Topic}/>
    </div>
  )
} 
```

但正如我们刚刚谈到的，因为`Topics`是由 React Router 渲染的，所以它被赋予了一个我们可以使用的`match`道具。我们将用`match.url`替换`Link`的`/topics`部分，用`match.path`替换`Route`的`/topics`部分。

```
function Topics ({ match }) {
  return (
    <div>
      <h1>Topics</h1>
      <ul>
        {topics.map(({ name, id }) => (
          <li key={id}>
            <Link to={`${match.url}/${id}`}>{name}</Link>
          </li>
        ))}
      </ul> 
      <hr />

      <Route path={`${match.path}/:topicId`} component={Topic}/>
    </div>
  )
} 
```

🕺.干得好。

此时，我们的应用程序已经完成了一半。我们还需要增加几层嵌套。这里有一个好消息——在本教程中你不会学到更多的东西。我们需要做的是完成我们已经讨论过的剩余嵌套路由。我们将继续创建新的嵌套导航条，继续渲染`Route` s，我们将继续使用`match.url`和`match.path`。如果你在这一点上感到舒服，剩下的就是肉汁了。

现在，正如我们最初对`Topics`组件所做的那样，我们想让`Topic` (no s)也能呈现一个嵌套的导航条和一个`Route`。唯一的区别是现在我们深入了一个层次，所以我们将为我们的`Link`映射主题的`resources`，我们的`Route`将匹配`/topics/:topicId/subId`。

```
function Resource () {
  return <p>RESOURCE</p> }

function Topic ({ match }) {
  const topic = topics.find(({ id }) => id === match.params.topicId)

  return (
    <div>
      <h2>{topic.name}</h2>
      <p>{topic.description}</p> 
      <ul>
        {topic.resources.map((sub) => (
          <li key={sub.id}>
            <Link to={`${match.url}/${sub.id}`}>{sub.name}</Link>
          </li>
        ))}
      </ul> 
      <hr />

      <Route path={`${match.path}/:subId`} component={Resource} />
    </div>
  )
} 
```

最后，我们需要做的最后一件事是完成我们的`Resource`组件。因为这是最后一个子组件，我们将不再呈现更多的`Link`或`Route`，取而代之的是，我们将只给它一个基本的 UI，包括资源的名称、描述和一个(普通的)链接。

```
function Resource ({ match }) {
  const topic = topics.find(({ id }) => id === match.params.topicId)
    .resources.find(({ id }) => id === match.params.subId)

  return (
    <div>
      <h3>{topic.name}</h3>
      <p>{topic.description}</p>
      <a href={topic.url}>More info.</a>
    </div>
  )
} 
```

这是完整的代码。

```
import React, { Component } from 'react'
import {
  BrowserRouter as Router,
  Route,
  Link,
} from 'react-router-dom'

const topics = [
  {
    name: 'React Router',
    id: 'react-router',
    description: 'Declarative, component based routing for React',
    resources: [
      {
        name: 'URL Parameters',
        id: 'url-parameters',
        description: "URL parameters are parameters whose values are set dynamically in a page's URL. This allows a route to render the same component while passing that component the dynamic portion of the URL so it can change based off of it.",
        url: 'https://tylermcginnis.com/react-router-url-parameters'
      },
      {
        name: 'Programatically navigate',
        id: 'programmatically-navigate',
        description: "When building an app with React Router, eventually you'll run into the question of navigating programmatically. The goal of this post is to break down the correct approaches to programmatically navigating with React Router.",
        url: 'https://tylermcginnis.com/react-router-programmatically-navigate/'
      }
    ]
  },
  {
    name: 'React.js',
    id: 'reactjs',
    description: 'A JavaScript library for building user interfaces',
    resources: [
      {
        name: 'React Lifecycle Events',
        id: 'react-lifecycle',
        description: "React Lifecycle events allow you to tie into specific phases of a components lifecycle",
        url: 'https://tylermcginnis.com/an-introduction-to-life-cycle-events-in-react-js/'
      },
      {
        name: 'React AHA Moments',
        id: 'react-aha',
        description: "A collection of 'Aha' moments while learning React.",
        url: 'https://tylermcginnis.com/react-aha-moments/'
      }
    ]
  },
  {
    name: 'Functional Programming',
    id: 'functional-programming',
    description: 'In computer science, functional programming is a programming paradigm—a style of building the structure and elements of computer programs—that treats computation as the evaluation of mathematical functions and avoids changing-state and mutable data.',
    resources: [
      {
        name: 'Imperative vs Declarative programming',
        id: 'imperative-declarative',
        description: 'A guide to understanding the difference between Imperative and Declarative programming.',
        url: 'https://tylermcginnis.com/imperative-vs-declarative-programming/'
      },
      {
        name: 'Building User Interfaces with Pure Functions and Function Composition',
        id: 'fn-composition',
        description: 'A guide to building UI with pure functions and function composition in React',
        url: 'https://tylermcginnis.com/building-user-interfaces-with-pure-functions-and-function-composition-in-react-js/'
      }
    ]
  }
]

function Resource ({ match }) {
  const topic = topics.find(({ id }) => id === match.params.topicId)
    .resources.find(({ id }) => id === match.params.subId)

  return (
    <div>
      <h3>{topic.name}</h3>
      <p>{topic.description}</p>
      <a href={topic.url}>More info.</a>
    </div>
  )
}

function Topic ({ match }) {
  const topic = topics.find(({ id }) => id === match.params.topicId)

  return (
    <div>
      <h2>{topic.name}</h2>
      <p>{topic.description}</p> 
      <ul>
        {topic.resources.map((sub) => (
          <li key={sub.id}>
            <Link to={`${match.url}/${sub.id}`}>{sub.name}</Link>
          </li>
        ))}
      </ul> 
      <hr />

      <Route path={`${match.path}/:subId`} component={Resource} />
    </div>
  )
}

function Topics ({ match }) {
  return (
    <div>
      <h1>Topics</h1>
      <ul>
        {topics.map(({ name, id }) => (
          <li key={id}>
            <Link to={`${match.url}/${id}`}>{name}</Link>
          </li>
        ))}
      </ul> 
      <hr />

      <Route path={`${match.path}/:topicId`} component={Topic}/>
    </div>
  )
}

function Home () {
  return (
    <h1>
      Home.
    </h1>
  )
}

class App extends Component {
  render() {
    return (
      <Router>
        <div style={{width: 1000, margin: '0 auto'}}>
          <ul>
            <li><Link to='/'>Home</Link></li>
            <li><Link to='/topics'>Topics</Link></li>
          </ul> 
          <hr />

          <Route exact path='/' component={Home} />
          <Route path='/topics' component={Topics} />
        </div>
      </Router>
    )
  }
}

export default App 
```

恭喜你。现在，您可以使用 React Router 创建嵌套路由了。告诉你妈妈，她会很骄傲的。🏅