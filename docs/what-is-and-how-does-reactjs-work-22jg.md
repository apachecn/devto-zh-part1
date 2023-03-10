# ReactJS 是什么以及它是如何工作的？

> 原文：<https://dev.to/amitkhirale/what-is-and-how-does-reactjs-work-22jg>

当选择在新项目的前端使用哪种技术时，我们面临着一个微妙而重要的决定，这将极大地影响我们应用程序的未来，因此选择补充和促进开发的技术是很重要的。脸书最近发布了 ReactJS，这是一个开源的 Javascript 库，这让他们在一些平台的开发中受益匪浅，例如 Instagram。尽管这个库有一些有争议的细节，但它在性能、模块化方面提供了很大的好处，并促进了非常清晰的数据和事件流，促进了复杂应用程序的规划和开发。

[![about react](img/6e509b715b6913af937e9f1bed36663c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xJ4GUKga--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rc3cm0gfugouhl5xf6mg.png)

需要注意的是，ReactJS 是一个专注于可视化的库。如果我们正在开始一个项目，我们可以建立在 Flux 架构上，但是如果我们已经有一个使用像 AngularJS 这样的 MVC 框架的项目，我们可以让 AngularJS 作为控制器，ReactJS 负责视图。这是有意义的，因为 ReactJS 在操作 DOM 时有更好的性能，这在处理我们的可视化中不断变化的长列表时有很大的影响。

[![about reactflowchart](img/c3321d1aa669d201abfd5381c7d0b602.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZEGbJ5Fb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c1rkt8xjfh71m4nhs1qb.png)

react 具有非常高的性能的秘密在于，它实现了一种称为虚拟 DOM 的东西，而不是像通常所做的那样在每次更改时呈现整个 DOM，它在内存副本中进行更改，然后使用一种算法将内存副本的属性与 DOM 版本的属性进行比较，从而只在不同的部分应用更改。这听起来可能需要做很多工作，但实际上这比传统方法更有效。我们在界面中有一个 2000 个元素的列表，发生了 10 次更改，更有效的方法是应用 10 次更改，找到属性发生更改的组件并呈现这 10 个元素，应用 10 次更改并呈现 2000 个元素。

有更多的步骤来规划和编程，但它提供了更好的用户体验和非常线性的规划。ReactJS 的一个重要特性是，它促进了数据的单向流动，而不是现代框架中典型的双向流动，这使得在复杂的应用程序中更容易规划和检测错误，在复杂的应用程序中，信息流可能会变得非常复杂，导致难以定位的错误，这可能会使生活变得非常悲惨。

希望这能让你在 React 上领先一步，并帮助你在创建 Web 应用程序时避免一些更常见的错误。