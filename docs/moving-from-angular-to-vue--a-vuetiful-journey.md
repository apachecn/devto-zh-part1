# 从 Angular 到 Vue:一次充满挑战的旅程

> 原文：<https://dev.to/hemantisme/moving-from-angular-to-vue--a-vuetiful-journey>

我在 SaaS 的一个产品上工作，这个产品有几个前端门户，它们都是建立在 [Angular 1](https://angularjs.org/) 之上的。经过两年多的生产，Angular 现在显示出它的年龄，它必须处理的不断增长的数据也没有帮助(> 2000 watchers，这有时是不可避免的，并且性能下降得比在你的 [2009 机器](https://www.amazon.com/Dell-15-6-Inch-Processor-Discontinued-Manufacturer/dp/B002PY7OSA)上运行 [FIFA 16](https://www.origin.com/ind/en-us/store/fifa/fifa-16/standard-edition) 时更糟糕😩).

大约 6 个月前，我们不得不重写一个核心前置插件，我们决定尝试 [Vuejs](https://vuejs.org/) 。基于[官方文档](https://vuejs.org/v2/guide/)和一些 hello worlds，它似乎很适合我们的用例。此外，Angular 已经被强调为未来的一个风险，我们正在积极寻找它的替代品。这感觉像是 Vue 的完美试验场。

被替换的插件是一个表单生成器/渲染器，它必须处理一些带有复杂规则和验证的大型表单。很多都需要使用 if/show/hide DOM 操作和过滤器重复。Angular 做得很好。这不是一个性能重写，而是一个结构。但有时 Angular 确实表现得出人意料。表单是用户生成的，有时所有的 DOM 操作和对巨大的、设计不良的表单的积极观察(*能力越强责任越大*)创造了我们所谓的*边缘案例*，所以我们也有一些东西要在角度前沿解决。

在经历了多年的消瘦之后，第一次和 Vue 一起工作真的是一种新鲜的感觉。此时，性能已经成为 Angular 的一个至关重要的问题，以至于每个双向绑定都不得不受到质疑。最初感觉不可思议的 Angular 的各个方面随着时间的推移显示出了它们的真实性，在某个时刻，魔法被打破了。

Vue 的开始是谨慎的。从 Angular 学到的经验起到了一定作用。但是没多久我就意识到这是一种完全不同的动物。在句法上，Vue 可能看起来和 Angular( *很相似，主要是因为它使用了 v-for、v-repeat、v-if、v-show、v-hide 等。*)这是因为有意选择不重新发明轮子，而是在引擎盖下，事情只是变得更好，与 Vue(更多检查[这](https://vuejs.org/v2/guide/comparison.html#Angular-1)了)

一旦意识到这种区别，做事的棱角方式就被抛弃，我们重新开始。就在那时，这段漫长的旅程真正开始了。

由于 Vue 使用的是虚拟 DOM(很像 [React](https://facebook.github.io/react/) ),所以大多数与绑定/观察器相关的问题都得到了解决。Vue 在优化 DOM 操作方面走得更远，与 React 相比，它只重新渲染受影响的组件，而 React 则重新渲染整个组件子树。

有了这个新的视角，插件的重写令人兴奋，进展顺利。我们做的第一个 Vue 项目已经平稳运行了 4 个多月，那些*边缘案例*还没有被报道。处理大量数据时的性能问题已经成为过去。

但这是一个做特定工作的小插件。我们不必为诸如路由、状态管理等更复杂的问题费心。由于我们渴望在 Vue 上做更多的工作，一个长期悬而未决的决定是用 Vue 替换 Angular 用于前端服务，其中一个门户被选为在 Vue 中重写。

在开始之前，我们制作了一个 hello world-ish 应用程序(实现[web pack](https://webpack.github.io/)+[vue](https://vuejs.org/)+[vue-loader](https://github.com/vuejs/vue-loader)+[vue-router](https://router.vuejs.org/en/)+[vuex](https://vuex.vuejs.org/en/))来确认计划实现的可行性。没多久就明白并实现了一切。我必须承认，在我尝试过的所有 js 框架中，Vue 的学习曲线是最温和的。如果你不需要太多，它可以像 jQuery 一样简单；如果你不需要了解构建系统、JSX、ES2015 等先决条件，它可以像 React 一样强大。hello world-ish 应用程序做起来很有趣，到目前为止，没有人会回到 Angular。

开发进行得很顺利，虽然文档还需要一些工作，但是社区很棒。Vue Gitter 频道是一个有生命的东西，需要一些伟人的帮助(一如既往，真正的英雄是无名的)。**我们的第一个 Vue 驱动的前端上周末上线**，到目前为止我们只有好消息要报告。性能让每个人都想从仍然棱角分明的动力前端得到更多，计划已经开始用 Vue 取代它们。更多信息请见下文。

就个人而言，我真的很喜欢 Vue。现在/过去有几个 js 框架，每个都有自己的优点和缺点，Vue 从它们那里获得了很多灵感，同时也为它们引入的许多现有问题提供了正确的答案。对于数据绑定，Angular 使用脏检查来跟踪更改，这有很大的性能损失，并且是其性能问题的最大原因，Vue 使用依赖跟踪观察系统来跟踪更改，并了解受影响的组件。

感觉更流畅，限制更少。没有什么感觉是被迫的，因为你总是知道你可以用其他方式做你正在做的任何事情，并且你选择了适合你需要的正确方法。例如，如果你愿意，你可以用 React 的 [Redux](http://redux.js.org/) 替换 Vue 的官方状态管理库 Vuex。Vue 的组件系统也比 React 的 JSX 简单得多，但同样强大。和 Redux 一样，Vue 也支持 JSX。但它不会强迫你。任何有效的 HTML 都是 Vue 中的有效模板。所以你可以决定什么时候使用 HTML 标记，什么时候使用渲染函数。Vue 做得更好的另一件事是组件范围的 CSS。Vue 没有采用 CSS-in-JS 的方式，而是简单地使用一个限定了作用域的属性，通过向元素添加一个惟一的属性并将 CSS 绑定到 element+attribute，将 CSS 限定到组件的范围。Vue 有太多值得喜欢的地方，我仍然意识到并惊叹于 Vue 有规律地做事的方式。

**最后**我想说的是，如果你打算开始一个新项目或者重写一个现有的项目，**一定要给 Vue 一个机会**。希望这对你来说很有趣，因为它对我来说太多了，所以我已经成为 Vue 的福音传道者，写我的第一篇博客！

*本帖最初发表在[媒体](https://medium.com/@Hemantisme/moving-from-angular-to-vue-a-vuetiful-journey-c29842ab2039)T3 上*