# 深入测试风格化组件和反应 hoc

> 原文：<https://dev.to/alex_escalante/diving-into-testing-styled-components-and-react-hocs>

对于我最近的项目，在 JS 解决方案中使用非常好的 CSS 是有意义的，称为 [styled-components](https://github.com/styled-components/jest-styled-components) ，以及[快照测试](https://facebook.github.io/jest/docs/snapshot-testing.html)，这是一种保存和比较组件渲染(HTML 和 CSS)快照的方法，以防止意外的更改。其中一个测试可以代替其他几个测试来检查渲染的特定属性。

请注意，为了使用 Jest 和样式化组件获得漂亮的快照，您必须安装这个[附加包](https://github.com/styled-components/jest-styled-components)。只要阅读文档，并确保在像我一样使用 Enzyme 的情况下进行所需的配置。

最后，当我按照常规使用`shallow()`来测试我的按钮组件时，我写道:

```
it("renders correctly", () => {
  const renderedComponent = shallow(<Button {...props}>Click me!</Button>);
  expect(renderedComponent).toMatchSnapshot();
}); 
```

快照的输出看起来不像我期望的 HTML 或 CSS:

```
<Styled(styled.button)
  theme={
    Object {
      "bg": "white",
      "fg": "black",
    }
  }
>
  Click me!
</Styled(styled.button)> 
```

这最初让我感到困惑，但后来我意识到我们的`Button`组件是一个无状态的功能性高阶组件(HOC ),其工作方式如下:

```
export const Button = props => {
  // assemble some props
  // decide which button to create
  return props.primary
    ? <PrimaryButton {...ourProps}>
        {props.children}
      </PrimaryButton>
    : <RegularButton {...ourProps}>
        {props.children}
      </RegularButton>; }; 
```

由于我们使用的是`shallow()`渲染，我们就不进行组件的二级渲染了！所以，这就是 [`dive()`](https://github.com/airbnb/enzyme/blob/master/docs/api/ShallowWrapper/dive.md) 的用武之地。该功能:

> "浅层呈现当前包装器的一个非 DOM 子级，并返回结果周围的包装器."

原来我们的按钮组件下有两层，一层用于主按钮、图标按钮等，一个“基类”用于所有这些按钮。这意味着我们最终不得不做两次潜水，所以我们称之为`deepDive()`:

```
const deepDive = c => shallow(c).dive().dive();

it("renders correctly", () => {
  const renderedComponent = deepDive(<Button {...props}>Click me!</Button>);
  expect(renderedComponent).toMatchSnapshot();
}); 
```

事实上，如果您不知道您的组件将包装多少层，您可以使用递归来概括这个想法:

```
const dd = w => (typeof w.type() === "function" ? dd(w.dive()) : w); // recursive diving
const deepDive = c => dd(shallow(c)); 
```

看看`ShallowWrapper`上的`type()`函数是如何告诉我们是否已经达到了 DOM 级别，或者我们还能再次`dive()`的。

深潜之后，快照出来只包含 CSS 和基本的 HTML。我们现在可以自由自在地使用组件或它的任何子组件，因为我们知道没有任何视觉上的改变会被我们的测试忽略。

交互测试有效(我们只做基本的，点击和禁用等)，我们得到了 100%的覆盖率，因为每个组件层都得到了锻炼。