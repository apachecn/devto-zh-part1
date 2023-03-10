# 将属性类型反应到流代码模式

> 原文：<https://dev.to/kenneth_skovhus/react-proptypes-to-flow-codemod-4gh8>

我将展示如何使用 React PropTypes 自动转换您现有的代码库，以使用更强大的流注释。如果你已经看到了为什么这是有价值的，你可以跳到下一部分。👇🏻

<figure>[![Eye candy, because you deserve it (unsplash.com/@thekorus)](img/165c3e782d4bb57f986598507dad200c.png "Eye candy, because you deserve it (unsplash.com/@thekorus)")](///static/4822d2d94a48a54d885e84246876a4ac/6e525/flow-unsplash.jpg) 

<figcaption>养眼，因为你值得拥有(unsplash.com/@thekorus)</figcaption>

</figure>

如果说 React 是脸书开发声明式和可组合 UI 的入门药，那么 React PropTypes 让很多人开始进行类型检查。

PropTypes 记录给定组件的属性，并在开发过程中对属性进行运行时验证。我发现它们真的很有帮助，但是它们也有几个缺点。最值得注意的是，我们应该在打开浏览器之前捕捉错误。向静态类型检查问好。

为了对应用程序进行静态类型检查，可以使用 JavaScript 扩展，如 [Flow](https://flow.org/) 或 [TypeScript](https://www.typescriptlang.org/) 。在这篇博文中，我主要关注流，但是同样的方法也可以应用于 TypeScript。

## 毫无疑问(对我来说):构建时的静态类型检查(使用 Flow 或 TypeScript)远远优于运行时验证。

如果你是心流新手，[新文档确实很好](https://flow.org/)。设置好流之后，你现在可以开始类型注释你的组件状态和属性了。对于一个简单的组件，它可能看起来像:

```
/* 1) Using good old PropTypes */
function Button({ message }) =>
  <button>{message}</button>; 
Button.propTypes = {
  message: PropTypes.oneOfType([
    PropTypes.string,
    PropTypes.number,
    PropTypes.instanceOf(Message)
  ]).isRequired,
};

/* 2) Using Flow annotations */
type Props = {
  message: string | number | Message,
};

function Button({ message }: Props) =>
  <button>{message}</button>; 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止一切顺利。但是转换一个现有的代码库来使用流注释而不是 PropTypes 可能是乏味的…然而这对于一个 codemod 来说是一个完美的任务！

## 修改代码的时间

为了自动化 React PropTypes 到流注释的转换，我们使用了另一个由脸书推广的工具:[jscodeshift](https://github.com/facebook/jscodeshift)(JavaScript code mod toolkit)。

如果你对使用 codemods 进行自动化重构的概念不熟悉，我将推广我在 meetup 上做的一个演讲:[“使用 JS codemods 进行自动化重构的介绍(Copenhagen.js Meetup，2016 年 12 月)”](https://www.youtube.com/watch?v=eMI0UBav8Q4):

[![Introduction to automated refactoring with JS codemods (Copenhagen.js Meetup, December 2016)](img/6801c574177c181db06ccbaadc529130.png)T2】](https://www.youtube.com/watch?v=eMI0UBav8Q4)

说够了。是时候摆脱 PropTypes 了！原来 [Billy Vong](https://github.com/billyvg) 已经用[code mod-prop types-to-flow](https://github.com/billyvg/codemod-proptypes-to-flow)为我们做了很多艰苦的工作。

因此，要自动转换文件夹 my-components 中的所有组件:

*   `git clone https://github.com/billyvg/codemod-proptypes-to-flow`
*   `jscodeshift -t codemod-proptypes-to-flow/src/index.js my-components`
*   看没有 PropTypes！(尽管`createClass`，导入的和定制的 PropTypes 验证器还不被支持——到目前为止！)

我已经成功地在多个项目中使用了这个 codemod，它极大地简化了转换。现在 Flow 开始在你写代码的时候发现错误，而不是在代码执行的时候！🍷 🙌🏻

* * *

请注意，有些情况下您希望保留您的属性类型(可能与流注释一起):

*   库代码中的属性类型可以帮助用户并记录接口
*   如果您没有验证任何外部资源(如 API)的流定义的自动化测试，PropTypes 会非常有用。为此，我推荐使用[babel-plugin-flow-react-proptypes](https://github.com/brigand/babel-plugin-flow-react-proptypes)在构建时添加 PropTypes
*   PropTypes 对于 React 上的学习材料仍然很有用(不需要用 Flow/TypeScript 给新人增加负担)
*   PropTypes 可以比流类型检查器当前支持的更加灵活(例如，[验证一个数字在一个特定的范围内](https://twitter.com/Daniel15/status/851232924225556480)

* * *

*这篇博文是从[https://skovhus.github.io/react-prop-types-to-flow-codemod/](https://skovhus.github.io/react-prop-types-to-flow-codemod/)T3 交叉发布的*

*感谢[马契克·佩卡拉](https://twitter.com/penzington)和[麦兹·哈特曼](https://twitter.com/Mads_Hartmann)审阅这篇文章。*