# 使用重组创建可重用的高阶 React 组件

> 原文：<https://dev.to/scastiel/create-reusable-high-order-react-components-with-recompose-45io>

在 React 中编写可重用的组件是每个 React 开发人员都关心(或者应该关心)的事情。无论是创建一个样式化组件的工具箱，还是共同的行为，等等。

虽然我经常使用高阶组件(例如用 *React-Redux* ，但是直到最近我才听说他们提供了一个更容易编写可重用组件的好机会，特别是感谢一个伟大的库: [Recompose](https://github.com/acdlite/recompose) 。让我们用一个具体完整的例子来看看。

如果您以前从未听说过高阶组件(HOC ),只需知道基本上，HOC 是一个函数，它将组件定义(类或函数)作为参数，并返回一个新的组件定义，在第一个组件定义的基础上添加一些行为。它实际上是模式 [*装饰器*](https://en.wikipedia.org/wiki/Decorator_pattern) ，应用于 React 组件。

如果你想了解更多关于 HOCs 的信息，React 的网站有一个真正完整的页面。

一个非常简单的例子:

```
const addBorder = borderWidth => Component => props => (
  <div style={{ borderColor: 'black', borderStyle: 'solid', borderWidth }}>
    <Component {...props} />
  </div> )

const MyText = <p>Hello!</p> 
const MyTextWithBorder = addBorder(5)(MyText) 
```

Enter fullscreen mode Exit fullscreen mode

您将得到一个组件`MyTextWithBorder`，它将显示文本“Hello！”边框为 5 像素。这里，`addBorder`就是所谓的高阶分量。

HOC 的兴趣是什么？一个真正有用的模式是将几个组件共享的行为提取到可重用的功能中。如果您使用 React with Redux 和 React-Redux，您可能会使用 HOC `connect`将状态和动作映射到道具。

作为本文的完整示例，我们将使用 HOC 创建一个电话号码输入组件，它将:

*   仅接受数字、括号、破折号和空格作为输入(当用户键入它们时)；
*   当用户离开输入时格式化电话号码(在*模糊*事件时)。(我们将只处理北美电话号码:“(514) 555-0199”。)

<center>![Phone number input result](img/37d361dbc5e218f4b1fcf4ff7a348da2.png)</center>

注意，我们假设我们的输入将被控制，即使用`value`和`onChange`属性来知道要显示的文本以及如何更新它。我们还希望该值只包含电话号码的数字(“5145550199”)，而不关心格式，因此只使用数字来调用`onChange`回调(在`event.target.value`)。

为了使我们的 HOC 更容易编写和维护，我们将使用库*来重组*，它提供了大量的实用函数来创建 HOC。我们将在文章中看到其中的一些。

为了开发我们的组件，我们将创建两个可重用的 HOC，上面两点各一个。这意味着我们希望我们的最终组件被定义为:

```
const PhoneNumberInput = formatPhoneNumber(
  forbidNonPhoneNumberCharacters(props => <input {...props} />) ) 
```

Enter fullscreen mode Exit fullscreen mode

这是介绍我们将使用的第一个*重组*函数的好地方:`compose`。它把几个 HOC 合并成一个，这样我们可以写得更清楚:

```
const PhoneNumberInput = compose(
  formatPhoneNumber,
  forbidNonPhoneNumberCharacters
)(props => <input {...props} />) 
```

Enter fullscreen mode Exit fullscreen mode

因为我们希望我们的 HOC 尽可能的可重用(比如格式化电话号码之外的东西)，我们将使它们更加通用:

```
// Only keep digits, spaces, dashes and parenthesis
const forbiddenCharactersInPhoneNumber = /[^\d\s\-()]/g

// '5145551234' => '(514) 555-1234'
const formatPhoneNumber = value =>
  value.replace(/^(\d{3})(\d{3})(\d{4})$/, '($1) $2-$3')

// '(514) 555-1234' => '5145551234'
const parsePhoneNumber = formattedPhoneNumber =>
  formattedPhoneNumber.replace(/[^\d]/g, '').slice(0, 10)

const PhoneNumberInput = compose(
  formatInputValue({
    formatValue: formatPhoneNumber,
    parseValue: parsePhoneNumber
  }),
  forbidCharacters(forbiddenCharactersInPhoneNumber)
)(props => <input {...props} />) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们只需使用正确的参数，就可以重用我们的两个 HOC 来格式化金额、社会保险号和其他所有内容，您已经看到这将变得多么令人敬畏了吗？😉

真正有趣的一点是，这里我使用了基本的`<input>`组件，但是我们也可以使用任何组件，只要它使用了`value`、`onChange`和`onBlur`。所以我们可以想象用 React Native，或者 Material-UI 等来使用我们的电话号码输入。

好了，现在到了重要的部分，使用重组函数编写我们的两个 HOC。

## 第一个 HOC:只接受部分字符

这里的想法是，当输入值发生变化(`onChange`事件)时，我们将拦截该事件，从值中删除所有禁止的字符，然后用干净的值调用父值`onChange`。

这里我们将使用`withHandlers`函数添加新的事件处理程序作为封装组件的道具。好的一面是，我们可以访问我们的组件道具(这里我们将使用`onChange`)来创建我们的处理程序:

```
const forbidCharacters = forbiddenCharsRegexp =>
  withHandlers({
    onChange: props => event => {
      // Remember `onChange` prop is not required (even if
      // here nothing would happen if it´s not defined).
      if (props.onChange) {
        const value = event.target.value
        const cleanValue = value.replace(forbiddenCharsRegexp, '')
        // We don’t mutate original event, but we clone it and
        // redefine the event.target.value with cleaned value.
        const newEvent = {
          ...event,
          target: { ...event.target, value: cleanValue }
        }
        // We dispatch our event to parent `onChange`.
        props.onChange(newEvent)
      }
    }
  }) 
```

Enter fullscreen mode Exit fullscreen mode

请记住，我们从另一个组件创建的组件必须尽可能地符合第一个组件的接口。它应该接受相同类型的相同属性。

现在，如果我们想要创建一个只接受数字的数字字段，我们可以写:

```
const NumericField = forbidCharacters(/[^\d]/g)(props => <input {...props} />) 
```

Enter fullscreen mode Exit fullscreen mode

我们现在有了第一个 HOC 来禁止一些字符，现在让我们写第二个，稍微复杂一点，来格式化用户输入。

## 第二个 HOC:格式输入值

对于我们的第二个 HOC，我们将不得不使用一个本地内部状态来存储输入值，而不把它交给封装组件。记住，我们只想在焦点丢失时格式化输入值(*模糊*事件)。

Recompose 有一个非常简单的功能，给组件添加一个本地状态:`withState`。它将状态属性的名称(将作为 prop 提供给子组件)、更新该状态属性的函数 prop 的名称(也作为 prop 提供)及其初始值(静态值，或者将 props 作为参数并返回值的函数)作为参数。

要添加我们的状态，我们将写:

```
withState(
  'inputValue',
  'setInputValue',
  // formatValue is one of our HOC parameters
  props => formatValue(props.value)
) 
```

Enter fullscreen mode Exit fullscreen mode

很简单，对吧？😉

现在我们有了状态，我们必须在输入值改变时使用 update，所以我们将定义一个定制的`onChange`处理程序:

```
withHandlers({
  onChange: props => event => {
    props.setInputValue(event.target.value)
  },
  // ... 
```

Enter fullscreen mode Exit fullscreen mode

在*模糊*事件中，我们将格式化该值，调用父`onChange`和`onBlur`道具，并用格式化的值:
更新显示的值

```
 // ...
  onBlur: props => event => {
    // parseValue is the other parameter of our HOC
    const parsedValue = parseValue(props.inputValue)
    const formattedValue = formatValue(parsedValue)
    props.setInputValue(formattedValue)
    // We don’t mutate original event, but we clone it and
    // redefine the event.target.value with cleaned value.
    const newEvent = {
      ...event,
      target: { ...event.target, value: parsedValue }
    }
    if (props.onChange) {
      props.onChange(newEvent)
    }
    if (props.onBlur) {
      props.onBlur(newEvent)
    }
  }
) 
```

Enter fullscreen mode Exit fullscreen mode

我们的 HOC 的最后一步是确保只有被`<input>`组件接受的道具会被传递给它。为此，我们将使用 Recompose 的`mapProps`函数从现有道具中创建一个新的道具对象，并使用 *lodash* 的`omit`函数从一个对象中排除一些属性来创建一个新的:

```
mapProps(props => ({
  ...omit(props, ['inputValue', 'setInputValue']),
  value: props.inputValue
})) 
```

Enter fullscreen mode Exit fullscreen mode

用`compose`组装所有东西，我们会得到:

```
const formatInputValue = ({ formatValue, parseValue }) =>
  compose(
    withState('inputValue', 'setInputValue', props => formatValue(props.value)),
    withHandlers({
      onChange: props => event => {
        props.setInputValue(event.target.value)
      },
      onBlur: props => event => {
        const parsedValue = parseValue(props.inputValue)
        const formattedValue = formatValue(parsedValue)
        props.setInputValue(formattedValue)
        const newEvent = {
          ...event,
          target: { ...event.target, value: parsedValue }
        }
        if (props.onChange) {
          props.onChange(newEvent)
        }
        if (props.onBlur) {
          props.onBlur(newEvent)
        }
      }
    }),
    mapProps(props => ({
      ...omit(props, ['inputValue', 'setInputValue']),
      value: props.inputValue
    }))
  ) 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！我们有我们的两个高阶组件，我们可以用它们来创建我们的手机输入字段组件！下面您可以找到包含这个例子的完整源代码的 JSFiddle，并测试结果。不要犹豫叉 [JSFiddle](https://jsfiddle.net/scastiel/prme4k6L/) 玩重组或创建自己的高阶组件。

我希望这篇文章让您想了解更多关于重组和高阶组件的知识。我确信 HOCs 创造了一种编写可重用组件的新方法；毫无疑问，将来我们会越来越多地在这里看到他们😀。

一些可进一步发展的资源:

*   [重组 API 文档](https://github.com/acdlite/recompose/blob/master/docs/API.md)相当完整，尽管在我看来它缺少一些例子来理解一些复杂函数；
*   关于 HOCs 的 React 页面包含很多信息，比如你不应该对 HOCs 做什么😉
*   [深度反应高阶组件](https://medium.com/@franleplant/react-higher-order-components-in-depth-cf9032ee6c3e):HOCs 的绝佳入门
*   为什么潮人会重组所有东西:一个很酷的重组概念介绍(似乎有点过时……)

*这篇文章最初发表在我的博客上。*