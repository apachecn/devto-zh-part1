# 如何在 React.js 生态系统中导航而不迷路

> 原文：<https://dev.to/marius/how-to-navigate-the-reactjs-ecosystem-without-getting-lost-13bo>

<figure>[![](img/1f02007cf42ff5d716ea3d7ee8b5824b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hiKzYhKH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AaD0tWP4vXjrs1FTFlgkKAg.jpeg) 

<figcaption>[照片由盖瑞特·帕克](https://unsplash.com/photos/2jyxADdd2t4)</figcaption>

</figure>

在软件开发的世界里，通常很难找到学习新事物的直接途径。该不该看书？跟着教程走？看视频？求教？

有这么多可能的路径和选择，迷路是很容易的。

学习 React.js 实际上很简单，只需要知道走哪条路。

### **先决条件**

我建议你在开始学习 React 之前，先牢牢掌握以下一些东西。

<figure>[![](img/a220e1add78ddea766b4a5631d025eef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F9br-gI1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AJEaQ6urCMv9CWzrew__dTg.jpeg) 

<figcaption>[照片由爱丽丝·多诺万劳斯](https://unsplash.com/@alicekat?photo=z9F_yK4Nmf8)</figcaption>

</figure>

#### 确保你对 HTML 和 CSS 有所了解

几乎所有的网络开发都需要这两方面的知识。如果你是这个领域的绝对初学者，我会推荐你去看一下 Travis Neilson 的视频，比如 T2 的 HTML5 基础和 T4 的 CSS 基础。然后跳到[freeCodeCamp.org](https://www.freecodecamp.org)或[codeacademy.com](https://www.codecademy.com/learn/learn-html-css)进行一些练习。

你的最终目标应该是获得一个网页的基本设计或结构，将它转换成代码，并在视觉上看到你所希望的样子。理想情况下，它将看起来非常类似于你的设计。

#### 掌握好 JavaScript 本身

我不建议在花时间学习 JavaScript 语言之前尝试学习框架和库。根据你的经验，有大量的资源可以学习 JavaScript。

对于一个绝对的初学者来说， [freeCodeCamp](https://medium.freecodecamp.org/) 有一套令人敬畏的资源，包括一个包含所有 JavaScript 基础知识的[视频播放列表](https://www.youtube.com/playlist?list=PLWKjhJtqVAbk2qRZtWSzCIN38JC_NdhW5)，以及一个学习和练习的互动场所[就在他们的网站](https://www.freecodecamp.com)上。

学习代码最好的方法就是写代码！

如果你过去已经使用过 JavaScript，需要复习一下这个[再介绍](https://developer.mozilla.org/en-US/docs/Web/JavaScript/A_re-introduction_to_JavaScript)。

如果你不确定你是否知道 JavaScript，那么我打赌[你不知道 JS](https://github.com/getify/You-Dont-Know-JS) 。这实际上是一个很受欢迎的系列丛书，对那些想更好地理解这门语言的人很有帮助。

> “当你努力理解你的代码时，你就创造了更好的作品，并在你所做的事情上变得更好。代码不再只是你的工作，而是你的手艺。”
> 
> ——詹恩·卢卡斯

#### 除了学习基本的 JavaScript，你还需要对 ECMAScript 2015 (ES6)的一些特性有一个很好的理解

具体来说，重点了解以下内容:

*   模块(导入/导出)
*   让和 const
*   箭头功能
*   班级
*   解构
*   休息/伸展

这些肯定会出现在您的 React 代码中，对它们有一些了解会让您更容易地使用文档。

为了快速入门，我推荐 LearnCode.academy 的 [ES6 Cheatsheet](https://www.youtube.com/playlist?list=PLoYCgNOIyGACDQLaThEEKBAlgs4OIUGif) 或 [Babel 的资源之一](https://babeljs.io/learn-es2015/)。

#### 了解如何使用节点包管理器(npm)

[安装 Node.js](https://nodejs.org/en/) ，它会附带 npm。目前，npm 是下载 JavaScript 开发依赖项的最佳方式之一。

例如，这个简单的命令将允许您为一个项目安装和下载 React:

```
npm install --save react 
```

Enter fullscreen mode Exit fullscreen mode

您最初需要知道的大部分内容是如何安装包。仅这一点就能让你接触到大量的工具和库，让你事半功倍。

#### (可选)学习函数式编程的基础

虽然学习 React 不是必需的，但是理解基本的函数式编程概念在 React 开发中会派上用场。

我会推荐理解函数构成，纯度，高阶函数。这里的目标不是成为这个主题的专家，而是至少接触到它。简单介绍一下，这是一位伟大的[演讲者](https://www.youtube.com/watch?v=e-5obm1G_FY)的精彩演讲。如果你想的话，你也可以直接从你的[收件箱](https://medium.freecodecamp.com/learning-the-fundamentals-of-functional-programming-425c9fd901c6)中了解。

<figure>[![](img/0b3f78df653f859aaab0693b86cee01d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AsVLFleW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATc2fPHyukVN0I5UOEt_nGg.jpeg) 

<figcaption>[卢克·帕默尔摄影](https://unsplash.com/search/hiking?photo=KBpnPk44tOA)</figcaption>

</figure>

> "只要你坚持攀登，每座山顶都触手可及。"
> 
> ——**巴里·芬莱**

根据您的开发经验，您可以在**几个小时到几天**内学会 React 基础知识。除此之外，只需要多一点经验，你马上就能创建应用程序。

这怎么可能呢？嗯…

### 首先暂时不要学 Redux 或者其他库

很多人在第一次学习 React 时犯的最大错误是搜索一个初学者模板或一个已经包含 Redux 和一堆其他库的教程。

人们经常问我学习反应的最好方法是什么。出于某种原因，他们从来没有想到官方文档实际上是一个很好的起点，因为它专注于教你如何反应。

暂时忘掉 Redux。你甚至可能不需要它。

忘记其他的库和样板文件。

> “通过模仿样板文件来学习反应就像通过在高级餐厅吃饭来学习烹饪一样。它不起作用。你需要从基础开始，忽略掉错过的恐惧。”丹·阿布拉莫夫

#### **专注于做出反应和独自做出反应。**

我推荐这一点的原因和你在熟悉代数之前不想学习微积分是一样的。或者你可能甚至不需要微积分来解决一个简单的数学问题。

弄清楚 React 自己能为你解决什么问题，不能解决什么问题。这将为您提供一个基本的指导，让您知道什么时候应该引入更多的库，并最终在您的项目中引入更多要学习的东西。

### 以下是最简单的入门方法

从[创建-反应-应用](https://github.com/facebookincubator/create-react-app)开始。它将为您提供从小处着手所需的一切，而不会受到样板文件和配置的阻碍。

它允许您自由地专注于学习 React 本身，而不必担心学习和设置 Webpack、Babel 和各种其他配置。

```
npm install -g create-react-app 

create-react-app my-app

cd my-app 

npm start 
```

Enter fullscreen mode Exit fullscreen mode

这四个简单的命令将为您提供启动项目所需的一切。它包括当你修改代码时刷新浏览器的工具。

它还提供了一个 build 命令，可以将你的代码编译成一些静态资产，你可以很容易地将它们部署到任何地方，它还提供了一个很棒的[用户指南](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md)，可以指导你完成这个过程。

我认为 create-react-app 有点像登山鞋。你不一定需要登山靴来爬山，但它们肯定会有所帮助，甚至可能让你更容易爬上某些表面。如果你意识到你不需要它们，你可以随时把它们从你的脚中“弹出”。

随着工具的出现，让我们回到学习 React 的道路上来。

### 掌握 React 的基本原理

查看这篇关于 React 的主要[概念的文章。实际上没有很多东西你需要学习。](https://medium.freecodecamp.com/the-5-things-you-need-to-know-to-understand-react-a1dbd5d114a3)

一般来说，您应该了解以下内容:

*   JSX:是什么，它与传统 HTML 有何不同，以及如何声明性地设置它来处理动态变化

```
/* Notice how you have to use className instead of class 
    And how the expression inside the curly braces allow it to
    dynamically handle any name passed in via props */

<h1 className="greeting">Hello, {this.props.name}</h1> 
```

Enter fullscreen mode Exit fullscreen mode

*   了解如何编写功能性的无状态组件。[下面是为什么](https://hackernoon.com/react-stateless-functional-components-nine-wins-you-might-have-overlooked-997b0d933dbc)。

```
// These are really just simple functions which return JSX
function MyComponent(props) {   
   return <h1 className="greeting">Hello, {props.name}</h1>;  }

// Alternatively write them using arrow functions
const MyComponent = (props) => (<h1 className="greeting">Hello, {props.name}</h1>); 
```

Enter fullscreen mode Exit fullscreen mode

*   了解如何使用 ES6 类语法编写组件。它允许你编写更复杂的组件，访问生命周期钩子和本地状态

```
class MyComponent extends React.Component {   
   render() {     
     return <h1 className="greeting">Hello, {this.props.name}</h1>;   
   } 
} 
```

Enter fullscreen mode Exit fullscreen mode

*   确保你很好的理解了[状态](https://facebook.github.io/react/docs/state-and-lifecycle.html#using-state-correctly)以及如何正确的使用它。理解使用组件的本地状态的利与弊将为您提供何时使用、何时不使用不同的状态管理解决方案的思想基础
*   学习如何编写和使用[组件生命周期挂钩](https://facebook.github.io/react/docs/react-component.html#the-component-lifecycle)以及每个挂钩何时有用

```
class MyComponent extends React.Component {
   // A couple examples of hooks that I've had to use a lot:
   componentDidMount() { 
     /* useful for initializing or triggering events after the
          component renders to the DOM */                                
   }    

   shouldComponentUpdate() {
     /* useful for performance purposes and preventing redundant 
          re-rendering */
   }

   static getDerivedStateFromProps() {
     /* useful for when you need to trigger state changes when new props   
          come in */
   }

   render() {     
     return <h1 className="greeting">Hello, {this.props.name}</h1>;   
   } 
} 
```

Enter fullscreen mode Exit fullscreen mode

*   了解如何使用 [PropTypes](https://facebook.github.io/react/docs/typechecking-with-proptypes.html) 。这是向组件添加一些基本类型检查的简单方法

```
import PropTypes from 'prop-types';  
class MyComponent extends React.Component {   
   render() {     
     return <h1 className="greeting">Hello, {this.props.name}</h1>;   
   } 
}
Greeting.propTypes = { 
   name: PropTypes.string 
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 学习如何构建你的代码

一旦你有了基本原则，你就要开始思考你的代码是如何构造的。

了解容器和表示性[组件](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)的概念。这样做将帮助您理解如何在 React 代码中更好地分离关注点。

如果您决定将来加入一个状态管理解决方案，比如 Redux，那么容器组件将有助于这种转变。您将知道在您的应用程序中传递的大多数数据都来自容器。

如果你还没有，也考虑一下你的文件夹结构。随着代码库开始增长，考虑一下文件夹结构的伸缩性。

文件容易找到吗？

如果你和一个团队一起工作，他们能直观地知道具体的组件在哪里吗？

请注意，您不必立即将代码置于特定的结构中。当你学习这些概念的时候，试着养成重构和改进你的代码的习惯。

> “我不是一个伟大的程序员；我只是一个习惯很棒的好程序员。”
> 
> -肯特贝克

### 构建解决实际问题的应用程序

深入理解 React 的最好和最真实的方法是用它来构建一些东西。

试着做一些能真正激励你去做的事情，避免做一些你可能已经知道解决方法的事情。

*   尝试合并一些外部数据(可能进行 api 调用),并弄清楚如何让数据在应用程序中正确流动，充分利用状态和属性
*   一旦你的应用程序需要多个页面，考虑集成 react-router
*   使用一些组件库，以基本的外观快速启动并运行。要知道，我们与 npm 和 Github 一起拥有的这个令人惊叹的 JavaScript 生态系统允许您轻松地将现成的组件集成到您的应用程序中
*   在某个地方部署您的应用程序。有许多不同的方式可以免费部署您的代码。尝试一个。没有什么比看到你的作品被发布到互联网上，并能够与他人分享链接更酷的了

<figure>[![](img/217562aa5fdd51a09f5d9e591382ee23.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KgSTNnEr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Afdt1z1Z4aPjxLHu_PLrjFA.jpeg) 

<figcaption>[照片由卡伦·艾姆斯利](https://unsplash.com/@kalenemsley?photo=mgJSkgIo_JI)</figcaption>

</figure>

如果你已经走了这么远，那太棒了！回顾自己的进步，并以此为荣。

还想吃吗？这里有一些建议。

### 测试你的代码！

确保你尽早学会如何测试你的代码。使用 [Jest](https://facebook.github.io/jest/) (或者你最喜欢的试跑者)和[酵素](http://airbnb.io/enzyme/)。

Jest 和 Enzyme 相当容易学习，实际上可以帮助你理解你的组件是如何独立工作的。

最重要的是，测试使你的代码在重构和改进时更加安全，同时也作为你的组件的动态文档。

### 使用高级状态管理

你是否觉得 setState 的状态管理变得太复杂了？你是否经常将道具传递给多个层次的后代组件？

是时候学习 Redux 或另一种风格的 Flux 了！您需要了解状态管理系统带来的好处，以及何时使用或不使用它。

### 不重复自己

如果您发现自己在多个地方编写相同的代码，看看是否可以利用其他策略来重用逻辑。学习如何创建和使用[高阶组件](https://facebook.github.io/react/docs/higher-order-components.html)。这是一种重用组件逻辑的高级技术。它将提高你的知识，当谈到组件组成。

<figure>[![](img/8c02e158d7ded5fa61ca3fc8f0d18e03.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VBSP_8Eg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0C5l3f6I6Wv9BEDzK9DeHA.jpeg) 

<figcaption>[摄影 Justin Luebke](https://unsplash.com/@jluebke?photo=Gcl6jcB1r9g)</figcaption>

</figure>

### 向上调平

事实上，这个列表永远不会结束。

你可以不断学习新的东西来改进和增加你的应用和技能。

如果你专注于基础，并且做到了上面的大部分，我相信你不仅在 React 上，而且在前端和 JavaScript 开发上都已经走上了一条好的道路。

JavaScript 生态系统在不断变化。让自己保持**不断学习**的心态。不断尝试，找出最适合你的方法。

展望未来，你的经验将有助于指导你下一步该做什么。

感谢阅读！

* * *