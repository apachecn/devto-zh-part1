# 从 jQuery 插件到 React 开发的 7 个步骤

> 原文：<https://dev.to/dimaip/7-steps-from-jquery-plugins-to-react-development-4p61>

> 注意，这个帖子来自 2017 年，可能感觉有点过时，但我相信主要思想仍然是相关的。

JavaScript 单页面应用程序(SPAs)在过去几年里有了巨大的飞跃。我们谈论他们，我们写他们，他们得到了所有你能想到的关注和议论。但即使在 2017 年，也不是所有东西都是 SPA。那么，当您需要向一个基本上静态的后端驱动的网站添加一些交互式小部件时，您会怎么做呢？对于所有这些滑块、标签、反馈表单等等，强制性的 jQuery 摆弄仍然是最好的方式吗？

在这里，我将向您概述 jQuery 插件和成熟的 SPA 之间的架构步骤。在每一步，你都要决定利弊得失，然后继续前进。诀窍是尽早停止，根据需要引入尽可能多的抽象，不要过多。

# 第一步。您可能不需要 jQuery

[![image](img/455e5e733f542a015498ad359448a6a2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--OxEJ2I4t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloud.githubusercontent.com/assets/837032/22975317/5cdfd962-f397-11e6-91e3-867508639da9.png)

我们应该做的第一件事是记住 jQuery 不再是 JavaScript 的同义词，没有它也是完全可能的。有一个很棒的网站可以证明这一点:[youmightnotneedjquery.com](http://youmightnotneedjquery.com/)啊，但是 jQuery 插件呢，我们肯定离不开它们吧？！嗯，不，实际上几乎肯定有一个普通的 JS 插件可以满足所有的需求，请看这里:【youmightnotneedjqueryplugins.com

优点:

*   刮掉几十 KB 的 JS 代码。不，这不仅仅是下载时间的问题，还有 JS 解析时间和执行时间的问题，这些在老式移动设备上确实很重要
*   离 DOM 和浏览器 API 更近一些。这将使你成为更好的 JavaScript 开发者

缺点:

*   更详细的低级代码。你失去了熟悉的抽象概念

# 第二步。切换到 ES6

ES6 很好，它对 JavaScript 进行了现代化处理，看起来像 Python 等其他时髦语言一样新鲜。箭头函数、析构、智能变量声明等。不过，不要对 ES6 类过于兴奋，它们仍然是 JS 基于原型的继承的包装器，所以在使用它们之前，一定要理解它们是如何工作的！不要担心浏览器支持，因为大多数 ES6 都可以通过 Babel 移植到 ES5。但是，是的，这需要一个额外的构建步骤。

优点:

*   更具表现力的语言

缺点:

*   额外构建步骤

# 第三步。使用 ES6 模块

比新的 ES6 语言构造更好的是能够将代码分解成彼此需要的模块。除此之外，您还可以从 npm 导入第三方代码。您可以使用 Webpack 将您的模块捆绑到一个文件中。使用 UMD 格式可以从浏览器或者作为一个 CommonJS 模块从 Node 或者从另一个 Webpack 应用程序使用生成的包。

优点:

*   不再需要手动通过脚本标签向全局范围添加额外的依赖项
*   您的依赖关系不会在全局范围内发生冲突
*   模块化代码更易于管理和重用，不再需要那些冗长的 js 文件

缺点:

*   需要配置 Webpack

# 第四步。视之为国家的职能

现在我们已经对 JavaScript 进行了一点现代化，让我们看看如何使用它的架构模式。

React 教给我们的最重要的事情是，将视图作为应用程序状态的函数是多么酷。这样，我们只有一条创建和更新视图的路径:render 函数。这对简单的 JS 小部件有用吗？太好了。不再需要通过一些属性从一个 html 标签的 soap 中获取某个 DOM 节点，并强制性地修改它！我给你举个例子。

首先，让我们定义一个小助手函数，它将帮助我们创建 DOM 节点。它的 API 应该是这样的:`el('h1', {class: 'headline'}, 'I'm a header')`，它将呈现这样的 DOM 节点:`<h1 class="headline">I'm a header</h1>`。我非常天真的实现看起来像是[这个](https://github.com/psmb/typo-reporter/blob/da7ffc6ec6b6d968d72cc4e2dbfa6a5229a2c3ff/src/el.js)，但是我相信你可以在几分钟内做得更好。

现在我们手头有了这样的助手，我们可以很好地呈现一些 DOM 节点作为我们状态的函数:

```
var rootNode = document.createElement('div');
document.body.appendChild(rootNode);

var state = {
  counter: 0
};

function setState(newState) {
  state = Object.assign({}, state, newState);
  // remount the whole node tree on every change
  mount();
}
function increment() {
  setState({counter: state.counter + 1});
}
function render(state) {
  return el('div', {}, [
    el('h1', {}, 'Demo'),
    el('button', {onClick: increment}),
    el('div', {class: state.counter > 10 ? 'plenty' : 'a-few'}, state.counter)
  ]);
}
function mount() {
  // sorry, delete children properly IRL
  rootNode.innerHTML = '';
  var renderedNodeTree = render(state);
  rootNode.appendChild(renderedNodeTree);
} 
```

同样，这也太天真了，但是我们得到了我们想要的:在每次更新状态时，我们重新呈现整个视图。最酷的部分是，我们的视图现在完全是声明性的，并且拥有 JavaScript 的全部能力:条件、循环、事件处理程序，任何想到的东西。

优点:

*   视为国家的一项职能。声明性呈现。用于更新和呈现的相同代码路径
*   没有依赖！没有额外的代码下载和解析！

缺点:

*   最终会变得缓慢。但是这并不是一开始就要担心的事情，在你解决问题之前不要做过早的优化！
*   元素在每次更新时都处于松散状态。对于输入字段来说，这是一个很大的问题！我们得做点什么！

# 第五步。虚拟 DOM

[![image](img/d07f32ff9c3858d08a9518254d31b812.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FvivNfS2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloud.githubusercontent.com/assets/837032/22975671/9e4b3d32-f398-11e6-9118-c1d05bce3774.png)

如果你被前一个解决方案的两个缺点中的一个击中，是时候继续前进了。我们所有的问题都来自于每次更新时的完全重新渲染，所以为了解决这些问题，我们需要一种方法来更新 DOM 元素，而不需要在没有必要的情况下重新创建它们。这就是虚拟 DOM 的用途。除了 DOM 节点本身之外，虚拟 DOM 还保存了 DOM 节点的虚拟表示。当要求更新状态时，它会呈现新的虚拟元素树，然后决定 DOM 中的哪些元素可以更新，哪些元素需要丢弃并重新创建。编写自己的虚拟 DOM 实现很有趣，但是我们现在不打算这么做。相反，我们可以选择现有的一个，例如[maquettejs.org](http://maquettejs.org)。它做它的工作来换取 3KB 的额外下载大小。

优点:

*   视图作为状态的一个函数，但是现在它可以很好地处理大量的节点，更重要的是它可以在更新时保持 DOM 元素的状态。

缺点:

*   增加了 3KB 的外部依赖性。你需要决定你是否能接受。

# 第六步。组件化

[![image](img/3a0017e8f0d428e3df893e71a42d82c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--f4LvmYC6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloud.githubusercontent.com/assets/837032/22975738/c2e929d8-f398-11e6-85e1-774f1db366bf.png)

我想我们已经得到了替换小型 jQuery 小部件所需的所有东西。但是如果你的部件开始增长，你可能想把它分解成更小的可重用组件。这正是 React 背后的思想，所以如果您已经做到了这一步，那么将您的小部件重构为 React 组件可能是有意义的。根据您的目标浏览器支持，通过使用 react 本身的一个克隆版本，例如 Preact，您可能会得到比 React 本身更轻量级的东西。Preact 的核心重量为 3KB，如果您在其上再添加 10KB，您将通过 preact-compat 垫片获得与现有 react 生态系统的兼容性。

优点:

*   使用设计良好的 API、道具验证和许多附加功能，将您的整体代码分解成更小的可重用组件。
*   利用巨大的 React 生态系统:借用组件，使用开发工具，性能优化工具等等。

缺点:

*   一旦状态分散在不同的组件中，共享和同步就变得更加困难

# 第七步。共享状态管理

[![image](img/10bd0a0d43a5a0707d99a80d5815e27d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--2wmddDXV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloud.githubusercontent.com/assets/837032/22975802/f92ca86c-f398-11e6-9400-151a6e809b0a.png)

因此，一旦我们将应用程序分割成可重用的小块，组件间同步共享状态就变得更加困难。通常，在 React 中，模式是将共享状态放在需要它的组件的最顶层，并通过 props 将状态传递下去。那些长长的道具链会成为管理的噩梦，于是 React 的多状态管理解决方案出现了。其中最简单的就是 Redux。这个想法真的很简单:你把整个应用程序的状态提取到你的应用程序的顶部，把它放到上下文中，并通过你的子组件的上下文中的一个帮助器函数来访问它。您通过分派动作来改变状态，并通过称为 reducers 的纯函数来处理它们。Redux 将花费您大约 5KB，但是我猜您甚至可以在更少的代码行中实现相同的逻辑。真正的 Preact+Redux 应用程序可能很容易适合 10KB 以下的 JS。

优点:

*   状态更新是声明性的，允许容易地跟踪代码中的更新(和错误)
*   Redux 本身有一个很好的插件和开发工具生态系统

缺点:

*   然而，又多了一个依赖，又多了一个需要掌握的抽象

# 步骤 x .超越 JavaScript

我不会在这里写得太详细，但是你要知道，在 JavaScript 之外还有一个 web 开发的世界。Redux 的灵感来源 Elm 和脸书的下一个玩具 [ReasonML](https://facebook.github.io/reason/) 就是其中的几个。静态类型检查、不变性和函数式编程范例是外层空间中可以期待的东西。但是我们已经离舒适的 jQuery shire 太远了，不是吗？

# 结论

记住，每一个抽象，每一个依赖都是有代价的。从基础开始，学习底层的 API，并逐渐爬上抽象的台阶。

最近，我不得不创建一个简单的[“报告页面上的错别字”](https://github.com/psmb/typo-reporter)小部件。我不得不在第 4 步停止，因为在我自己的 2KB 代码上添加 3KB 的虚拟 DOM 是不值得的。

即使你开发的是一个 2KB 的小部件，而不是脸书式的 SPA，你的代码也不一定很糟糕，你也不应该轻率地做出决定。

附言:如果你已经走了这么远，请看[这个演讲](https://www.youtube.com/watch?v=mVVNJKv9esE)。