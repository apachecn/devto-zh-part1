# JS 中的可组合对象验证

> 原文：<https://dev.to/emilios1995/composable-object-validations-in-js>

<figure>[![](img/174228803c01c257de5e856f336e4d49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3cgHAlhs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aub06dgVGDo7XeByUxON0-w.jpeg) 

<figcaption>【贝拉斯阿特斯】by me</figcaption>

</figure>

在我的日常工作中，我在一个项目中工作，该项目由一个 react-native 应用程序组成，该应用程序足够灵活，可以根据我们许多客户的需求进行配置。运行时，很多配置来自服务器。但是我们还需要在编译时配置很多东西，主要是原生代码中需要的资产和变量。

为此，我们有一个设置脚本，它接收 JSON 中的配置文件，并产生一些副作用。我意识到，如果这些事情中的一些出错了，它将会悄悄地并且致命地影响 JS 运行时。因此，我决定在进行任何更改之前实现 JSON 配置文件的验证功能，以确保每个配置字段都具有正确的类型。

我知道这会很复杂，因为有些字段是可选的，有些字段是必需的，但前提是设置了其他字段，等等。

幸运的是，我找到了完美的抽象来简化这一过程。我发现了[注视着](https://github.com/25th-floor/spected)。

我的第一条规则是这样的:

```
const isType = R.curry((type, value) => R.type(value) === type);
const rules = {
  version: [[isType('Number')], 'Version must be a number'],
  build: [[isType('Number')], 'Build must be a number'],
  appName: [[isType('String'), 'appName must be a string']]
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们正在利用权力和[拉姆达](http://ramdajs.com/)和[阿谀奉承](https://www.youtube.com/watch?v=iZLP4qOwY8I)来进行惊人的简单验证。但是，当然，这是一个简化版本。真实世界的代码有更多的规则，而且*一遍又一遍地写同样的信息似乎是我不应该做的事情。*

如果我能传递一个函数来生成错误消息就好了…这样的函数将接收字段名和它应该是的类型，并返回一个漂亮的错误消息，就像这样:

```
const typeMessage = (type, field) => `${field} should be a ${type}`; 
```

Enter fullscreen mode Exit fullscreen mode

查看 Spected 源代码，我发现错误消息可能已经是一个函数，但它没有传递字段名。因此，我提交了一份 [PR](https://github.com/25th-floor/spected/pull/89) 来实现这一点，瞧！一个简单的、超级可组合的验证的全新世界开始出现了！

```
const typeMessage = (type, field) => `${field} has to be a ${type}`;
const typeRule = type => [
  isType(type),
  (val, field) => typeMessage(type, field)
];
const isString = typeRule("String");
const isNumber = typeRule("Number")

const rules = {
  version: [isNumber],
  build: [isNumber],
  appName: [isString]
} 
```

Enter fullscreen mode Exit fullscreen mode

现在您应该对这种方法的优点深信不疑了，但是我将通过写可选字段来加强我的观点。如果配置文件缺少这些字段中的一个，没有问题。但是，如果它存在，我们仍然要确保类型是有效的。

因为验证函数只是一个函数，所以我们可以创建一个高阶函数，它接受验证函数，并且只在值不为 null 时运行它。满足除非无:

```
const unlessNil = R.curry((f, val) => R.or(f(val), R.isNil(val))); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以这样做:

```
const optionalTypeRule = type => [
  unlessNil(isType(type)),
  (val, field) => `if ${field} is set, it must be a ${type}`
];
const validationRules = {
  appDescription: [optionalTypeRule('String')]
} 
```

Enter fullscreen mode Exit fullscreen mode

当然，为了做到这一点，首先输入对象必须被规范化，因此所有缺少的字段都添加了一个空值。这可以通过一个功能完成:

```
const normalize = (spec, input) =>
  Object.keys(spec).reduce(
    (acc, x) => R.assoc(x, R.propOr(null, x, input), acc),
    {}
  ); 
```

Enter fullscreen mode Exit fullscreen mode

太棒了，对吧？

现在，我将进入更疯狂的东西，继续阅读，如果你有兴趣使一个领域依赖于另一个领域！

因此，假设我们有一个配置字段`notificationsClientId`,只有当布尔字段`getsNotificationsis`为真时才需要它

一个奇怪的事实是，Spected 中的验证函数也将整个输入对象作为第二个参数。所以我知道这样的事情应该是可能的:

```
const validationRules = {
    notificationsClientId: [
    [
      dep("getsNotifications", R.equals(true), isType("String")),
      "If getsNotifications is true, notificationsClientId is required as a string"
    ]
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，dep 函数接受三个参数:

1.  当前字段所依赖的字段，
2.  在所述字段上运行的函数，
3.  以及在第二个参数中的函数返回`true`时在*当前*字段上运行的函数

然后 dep 返回一个可以使用的验证函数。这听起来可能很复杂，但是看看实现。很简单:

```
const dep = (field, cond, f) => (val, obj) =>
  cond(obj[field]) ? f(val) : true; 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

因此，希望这将帮助您了解组合友好的 API 和一般函数范式的一些优点。

感谢您的阅读！