# 苹果和橙子之战:Angular vs React vs VueJS

> 原文：<https://dev.to/roelofjanelsinga/the-battle-of-apples-and-oranges-angular-vs-react-vs-vuejs-3d3p>

[![The battle of apples and oranges: Angular vs React vs VueJS](img/db9c47796630953d336baf445f67f1d2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--G7yK-RfL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oxthfr19tf5r7rm1ikmr.jpg)

# 苹果和橘子之战:Angular vs React vs VueJS

人们一直在问用哪个框架(Angular，React，或者 VueJS)，哪个更好。我可以理解他们想知道项目中使用哪一个，但这是一个愚蠢的问题。这是在拿苹果和橘子做比较。我将解释为什么在特定的情况下应该使用一个而不是另一个，但这只是我的拙见。在我进入这个话题之前，它们都有它们的用途，没有一个比另一个更好。

## 棱角分明

在我开始使用 Angular 之前，我必须澄清我是偏向于它的。我使用 AngularJS 已经 3 年了，我在 Angular 4 上花了相当多的时间(只是从现在开始 Angular)。现在，我们将坚持 Angular，因为 AngularJS 并没有真正更新，它已经过时了。那么什么时候应该用 Angular 呢？当你构建一个中型到大型的应用程序时。设置时间比 React 和 VueJS 都长，但也是完整的包。React 和 VueJS 只用作用户界面，Angular 是用户界面，它还“包括”其他东西。我说包括在这里，但自从 Angular 版本 4 推出以来，它们已经从 Angular 核心中删除，并包括在单独的模块中。Angular 使用 TypeScript 而不是 Javascript，这对许多人来说是一个很大的障碍，但我真的很喜欢它。我提到过 Angular 主要适用于中型或大型应用程序，因为它不太容易用作“嵌入式”框架。你要么让所有页面都有棱角，要么一页都没有。

## 做出反应

正如我前面所说，React 只处理用户界面。
如果你想要路由器或任何与服务器交互的方式，
你需要自己找到模块，并将其与 React 集成。
很多人喜欢这个，因为它给了你选择你喜欢的模块的自由。React 可以作为一个嵌入式框架，所以你可以用 React 制作页面的一部分，而不是像 Angular 那样用一个框架制作整个页面。由于 React 可以很容易地用作嵌入式框架，而且还包括一个路由器模块，因此它可以用于小型到大型的应用程序。设置时间很短，但它有一个公平的学习曲线。Angular 使用 TypeScript，React 使用 JSX。这意味着所有的逻辑和模板都可以构建在一个文件中。

## VueJS

我想把 VueJS 称为“AngularJS 的所有优点”。
AngularJS 在我心中永远有一个特殊的位置，这就是为什么
我喜欢 VueJS 正在做的事情。VueJS 也是一个只处理用户界面的框架，就像 React 一样。它确实有一个路由器和模块来处理可用的服务器交互，所以以那种方式反应是相当类似的。它也是一个嵌入式框架，这意味着您可以将 VueJS 用于小型应用程序。我不建议将它用于中型或大型应用程序。这是一个新的框架，文件组织需要一些工作，因为它可能会变得混乱。这就是为什么我建议将它用于较小的应用程序。您可以轻而易举地设置它，因此您可以快速上手。VueJS 实际上使用的是普通的 Javascript，这一点我非常欣赏。除了 AngularJS 和 Angular 拥有的一些指令之外，没有什么新东西需要学习。

## 比较

我希望这能稍微澄清一下苹果和橘子之争。这些框架完全不同，甚至没有相同的用例。您可以将 Angular 用于大型应用程序，并且内置了所有最常用的模块。React 和 VueJS 都只用于用户界面，它们不包括任何处理服务器交互的模块。这意味着开发人员可以自由选择任何模块来填补这些空白。React 和 VueJS 是可比较的，因为它们都只用于用户界面，但它们仍然不服务于相同的用例。React 适用于从小型到大型的应用程序，因为文件组织比 VueJS 简单。VueJS 目前只适用于小型应用程序，因为它还没有成熟的时间。您可以使用这些框架中的任何一个来制作单页面应用程序，或者为一些动态元素使用 React 和 VueJS。

如果你想进一步谈论这个话题，请在 twitter 上关注我。