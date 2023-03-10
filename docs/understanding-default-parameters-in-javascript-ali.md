# 理解 Javascript 中的默认参数

> 原文：<https://dev.to/kepta/understanding-default-parameters-in-javascript-ali>

[![](img/fa06d54eba49c221f14e568379858bdb.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--aa_gtUDJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AkFFR-EddaPhP7B9qmOUFww.png)

ES2015 支持原生[默认参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters)。与人们的想法相反，它不仅仅是对`||`(逻辑 or 操作符)的语法替换。

### Javascript 前默认参数

在对默认参数的本地支持之前，应该这样写:

```
function filterEvil(array, evil) {
  evil = evil || 'darth vader';
  return array.filter(item => item !== evil);
} 
```

Enter fullscreen mode Exit fullscreen mode

要理解上面截取的代码，需要记住以下几点:

*   **Truthy/Falsy**:JavaScript 中的值可以分为 [truthy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) 或 [falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) 。简而言之，Javascript 试图将一个变量转换成一个布尔值，以找出条件(例如，if，for 循环)。* **短路求值**:涉及`||`的表达式从左到右处理。当从左向右处理时，编译器足够聪明，如果它遇到一个**真值**又名[短路](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Short-circuit_evaluation)，它不会处理剩余的项目。

简而言之，这只是一个语法糖:

```
function filterEvil(array, evil) {
  if (!evil) {
    evil = 'darth vader';
  }
  return array.filter(item => item !== evil);   
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法如此流行的原因是你可以链接多个`||`，让短路来处理它。想象一下用 **if 语句**写同样的东西。

现在如果你的**恶**变量恰好是空字符串`''`或者任何**假值**的话，上面的函数会假设它是`darth vader`。

这在大多数情况下是可以的，但是在更复杂的应用程序中，这可能会导致那些可怕的无声错误。这个问题的一个很好的解决方案是在语言内部，它被称为**默认参数**。

### Javascript 后默认参数

让我们使用同一个 **filterEvil** 示例，看看它在默认参数下会是什么样子。

```
function filterEvil(array, evil = 'darth vader') {
  return array.filter(item => item !== evil);   
} 
```

Enter fullscreen mode Exit fullscreen mode

乍一看，很整洁简洁。但是不要让外表欺骗你！引擎盖下有很多东西。认为缺省值会在没有提供`evil`时介入是轻率的。

让我们看看一些重要的细节，

### 1。null 和 undefined 之间的区别。

对于**不** **呈现**的问题，Javascript 有两个答案，`null`和`undefined`。(`null`成为[争议](https://www.lucidchart.com/techblog/2015/08/31/the-worst-mistake-of-computer-science/)的话题)。普遍的共识是`null`是一个显式的值，用来告知没有值。在下面的例子中，我们试图将`null`作为参数传递。

```
const list = [ 'luke', 'leia', 'darth vader' ];
filterEvil(list, null); // [ 'luke', 'leia', 'darth vader' ] 
```

Enter fullscreen mode Exit fullscreen mode

> 当提供有`null`或任何错误值时，filterEvil 函数**不会用默认值替换** `evil`。

当且仅当`evil`为`undefined`时，filterEvil 函数将替代。现在你很幸运，因为如果你不显式地传递一个参数:
，Javascript 将自动使用`undefined`

```
const list = [ 'luke', 'leia', 'darth vader' ];
filterEvil(list); // [ 'luke', 'leia' ] 
```

Enter fullscreen mode Exit fullscreen mode

这是在处理严重依赖于`null`的项目时要记住的事情。*(虽然，我很担心那些互换使用 *`null`* / *`undefined`* 的开发者)*

### 2。从左到右计算

默认参数从左到右计算。这确实令人困惑，但却是一个非常强大的功能。让我们看一个例子。

```
 function findEvil(array, evil = 'darth vader', respect = 'Bad ' + evil) {
  if (array.find(item => item === evil)) {
     return respect;
  }
}

findEvil(list); // Bad darth vader;

findEvil(list, 'luke'); // Bad luke; 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们可以重用左边的参数作为右边的默认参数。请注意，`respect`将获得应用了默认检查的`evil`参数。

### 3。调用函数

您还可以调用一个函数，并将返回的值用作默认参数。简而言之，这允许您调用一个常规函数并动态计算默认参数。

```
function whoIsEvilNow() {
  if (time > 2014) {
    return 'J. J. Abrams'
  }
  return 'darth vader';
}

function findEvil(array, evil = whoIsEvilNow()) {
  return array.find(item => item === evil);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 4。在调用时评估

现在这个功能是最让我困惑的。让我们看一个例子。令人困惑但非常强大的功能。让我们看一个例子。

```
function filterEvil(array = [], evil = 'darth vader') {
  return array.filter(item => item === evil)
} 
```

Enter fullscreen mode Exit fullscreen mode

> 每次调用不带参数的数组时，都会创建一个空数组的新实例。

每次不带参数调用 **filterEvil** 时，都会创建一个空数组的新实例。现在，如果你对[选择器](https://github.com/reactjs/reselect)和记忆化感兴趣，这可能会成为一个问题。这种行为很容易骗过你的依赖逻辑，让你以为事情发生了变化(我在跟你说话，你有什么反应)。例如，如果您在 react 项目中使用 reselect，您的组件可能会不必要地更新，因为 react 将使用空数组的每个新实例重新呈现。

### 以下是一些很酷的小技巧:

*   **必需参数检查:**您可以使用默认参数**强制**必需参数。在下面的例子中，我们将**邪恶**作为必需道具。

```
const isRequired = () => { throw new Error('param is required'); };

function filterEvil(array, evil = isRequired()) {
  return array.filter(item => item !== evil);   
} 
```

Enter fullscreen mode Exit fullscreen mode

*   **析构默认参数:**你也可以在析构表达式中使用默认参数。

```
function firstItem([first, second] = ['luke', 'skywalker']) {
  return first;
}

function findName({ name } = { name : 'darth' }) {
  return name;
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

我希望这篇文章能帮助你理解默认参数。

在 Twitter 上联系我 [@kushan2020](https://twitter.com/kushan2020) 。