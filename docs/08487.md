# JavaScript 中的语法过度

> 原文：<https://dev.to/kayis/syntactical-overkill-in-javascript>

这个月我开始了一个新的 [React-Native](https://facebook.github.io/react-native/) 项目，并使用了一些我在过去 12 个月左右只听说过的好的库。在写我的应用程序时，我情不自禁地注意到代码中出现的所有新语法。

首先，标准 JavaScript 的东西。在过去的几年里，它积累了许多新事物，如[解构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)或[异步/等待](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)。即使像这样简单的例子对新开发人员来说也是完全陌生的:

```
const {token, type} = await callApi(); 
```

Enter fullscreen mode Exit fullscreen mode

几年前，在 ES2015 和[承诺](https://developer.mozilla.org/de/docs/Web/JavaScript/Reference/Global_Objects/Promise) :
之前，这本书会有不同的写法

```
callApi(function(response) {
  var token = response.token;
  var type = response.type;
}) 
```

Enter fullscreen mode Exit fullscreen mode

正如我所说的，这只是一个日常 API 调用的简单例子。

然后我们得到了 [JSX](https://facebook.github.io/react/docs/jsx-in-depth.html) ，这是在 React 和 React-Native 中进行标记的默认方式。它基本上编译成了`React.createElement({...})`调用，但是看起来 JavaScript 有严重的问题。我的意思是，在做了几年 React 之后，它对我来说似乎不再那么糟糕了，你会发现你的标记在代码中相当快，但是，你也必须学习它是如何工作的。其他框架使用 Handlebars 之类的模板语言，把新概念带到了另一个层次，哈哈。

所以你写了这样的东西来创建一个组件:

```
const MyComponent = props => (
  <View>
    <Text>Hello!</Text>
    <Image src={require(...)} />
  </View> ); 
```

Enter fullscreen mode Exit fullscreen mode

与其这样:

```
var MyComponent = function MyComponent(props) {
  return React.createElement(View, null,
    React.createElement(Text, null, "Hello!"),
    React.createElement(Image, { src: require(...) })
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

这就是你在创建一个 React 应用程序时一天要写多次的东西。

接下来是 **CSS** 或者我应该说 CSS-in-JS？使用[样式化组件](https://styled-components.com/)来样式化你的 React 组件是现在要走的路，它使用了另一个新特性来将 CSS 转换成 JS，标记模板文字。

```
const DangerText = styled.Text`
  backgroundColor: red;
  color: white;
  fontWeight: bold;
`; 
```

Enter fullscreen mode Exit fullscreen mode

而不是像这样:

```
const style = {
  backgroundColor: "red",
  color: "white",
  fontWeight: "bold"
};
const DangerText = props => <Text style={style} {...props} />; 
```

Enter fullscreen mode Exit fullscreen mode

然后是 [GraphQL](http://graphql.org/) ，这是创建 HTTP APIs 的新方法。不要被我的讽刺所迷惑，这确实是构建 API 的好方法，我认为它比 REST 好得多，但是...幼兽...它有自己的查询语言，也利用了带标签的模板文字。

```
const query = gql`
  {
    user(id: 5) {
      firstName
      lastName
    }
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们在[流](https://flow.org/)的帮助下对整个事情进行静态类型检查。

```
function doSomething(func: <T>(param: T) => T) {
} 
```

Enter fullscreen mode Exit fullscreen mode

是的，我知道，过去我们有 CSS、HTML、JS 和 SQL，除了一些新的 JS 特性和 GQL 语法，这里没有太多根本性的新变化，但我必须承认，当你刚刚学习 JavaScript 时，或者即使你从上一次学习 JavaScript 以来已经停顿了几年，深入这样一个代码库可能需要相当大的努力。

这真的是应该走的路吗？我们应该用 JavaScript 更习惯地做事情吗？更多的对象文字而不是 CSS？更多的超脚本而不是 JSX？我不知道这样会不会更好，但是这样会减少很多使用的概念。