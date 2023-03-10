# 为什么在 JavaScript 中使用静态类型？(第二部分)

> 原文：<https://dev.to/iampeekay/why-use-static-types-in-javascript-part-2>

## **静态类型的优点**

编写程序时，静态类型为我们提供了许多好处。让我们探索其中的几个:

#### 1。尽早检测到错误和错误

静态类型检查允许我们在不实际运行程序的情况下验证我们指定的不变量是否为真。如果有任何违反这些不变量的情况，它们将在运行之前被发现，而不是在运行期间。

一个简单的例子:假设我们有一个简单的函数，它取半径并计算面积:

```
const calculateArea = (radius) => 3.14 * radius * radius;

var area = calculateArea(3);
// 28.26 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们要通过一个不是数字的半径(例如‘我是邪恶的’)……

```
var area = calculateArea('im evil');
// NaN 
```

Enter fullscreen mode Exit fullscreen mode

…我们会回到`NaN`。如果某个功能依赖于这个`calculateArea`函数总是返回一个数字，那么这个结果可能会导致一个 bug 或者崩溃。那不是很令人愉快，是吗？

如果我们使用静态类型，我们可以为函数指定确切的输入和输出类型:

```
const calculateArea = (radius: number): number => 3.14 * radius * radius; 
```

Enter fullscreen mode Exit fullscreen mode

现在尝试向我们的`calculateArea`函数传递除数字之外的任何东西，Flow 将向我们发送一条非常方便的消息:

```
calculateArea('Im evil');
^^^^^^^^^^^^^^^^^^^^^^^^^ function call
calculateArea('Im evil');
              ^^^^^^^^^ string. This type is incompatible with 

const calculateArea = (radius: number): number => 3.14 * radius * radius;
                               ^^^^^^ number 
```

Enter fullscreen mode Exit fullscreen mode

现在我们保证了函数只接受有效的数字作为输入，并返回有效的数字作为输出。

因为类型检查器会在您编码时告诉您什么时候有错误，这比在代码交付给您的客户后才发现错误要方便得多(也便宜得多)。

#### 2。活文档

类型为我们自己和代码的其他用户提供了活生生的文档。

为了了解如何实现，让我们来看看这个方法，我曾经在我工作的一个大型代码库中找到过这个方法:

```
function calculatePayoutDate(quote, amount, paymentMethod) {
  let payoutDate;

  /* business logic */

  return payoutDate;
} 
```

Enter fullscreen mode Exit fullscreen mode

第一眼(以及第二次和第三次)，我不知道如何使用这个功能。

是引用一个`number`？还是一个`boolean`？支付方式是一种`object`吗？又或许是代表支付方式类型的`string`？该函数是否以`string`的形式返回日期？还是作为`Date`对象？

不知道。

我当时的解决方案是通过代码库评估业务逻辑和 grep，直到我弄明白为止，但是仅仅理解一个简单的函数是如何工作的就需要做大量的工作。

另一方面，如果我们写了这样的代码:

```
function calculatePayoutDate(
  quote: boolean,
  amount: number,
  paymentMethod: string): Date {
  let payoutDate;

  /* business logic */

  return payoutDate;
} 
```

Enter fullscreen mode Exit fullscreen mode

函数将哪种类型的数据作为输入，将哪种类型的数据作为输出返回，这一点变得非常清楚。这演示了我们如何使用静态类型来传达函数的意图。我们可以告诉其他开发者我们对他们的期望，也可以看到他们对我们的期望。下次有人去用这个功能，就不会有问题问了。

有一种观点认为添加代码注释或文档可以解决同样的问题:

```
/*
  @function Determines the payout date for a purchase
  @param {boolean} quote - Is this for a price quote?
  @param {boolean} amount - Purchase amount
  @param {string} paymentMethod - Type of payment method used for this purchase
*/
function calculatePayoutDate(quote, amount, paymentMethod) {
  let payoutDate;
  /* .... Business logic .... */

  return payoutDate;
}; 
```

Enter fullscreen mode Exit fullscreen mode

这个管用。但它要冗长得多。除了冗长之外，像这样的代码注释很难维护，因为它们不可靠并且缺乏结构——一些开发人员可能写了很好的注释，一些可能写了模糊的注释，还有一些可能根本就忘了写。

当你重构时，很容易忘记更新它们。然而，类型注释有定义好的语法和结构，永远不会过时——它们被编码到代码中。

#### 3。减少复杂的错误处理

类型有助于消除复杂的错误处理。让我们再来看看我们的`calculateArea`函数是如何实现的。

这一次，我将让它获取一个半径数组，并计算每个半径的面积:

```
const calculateAreas = (radii) => {
  var areas = [];
  for (let i = 0; i < radii.length; i++) {
    areas[i] = PI * (radii[i] * radii[i]);
  }

  return areas;
}; 
```

Enter fullscreen mode Exit fullscreen mode

这个函数可以工作，但是不能正确处理无效的输入参数。如果我们想确保正确处理输入不是有效数字数组的情况，我们最终会得到一个类似于:
的函数

```
const calculateAreas = (radii) => {
  // Handle undefined or null input
  if (!radii) {
    throw new Error("Argument is missing");
  }

  // Handle non-array inputs
  if (!Array.isArray(radii)) {
    throw new Error("Argument must be an array");
  }

  var areas = [];

  for (var i = 0; i < radii.length; i++) {
    if (typeof radii[i] !== "number") {
      throw new Error("Array must contain valid numbers only");
    } else {
      areas[i] = 3.14 * (radii[i] * radii[i]);
    }
  }

  return areas;
}; 
```

Enter fullscreen mode Exit fullscreen mode

哇哦。对于一点点功能来说，代码太多了。

但是对于静态类型，我们可以简单地做:

```
const calculateAreas = (radii: Array<number>): Array<number> => {
  var areas = [];
  for (var i = 0; i < radii.length; i++) {
    areas[i] = 3.14 * (radii[i] * radii[i]);
  }

  return areas;
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，这个函数实际上看起来就像它最初的样子，没有了错误处理带来的视觉混乱。

很容易看到好处，对吧？:)

#### 4。更自信地重构

我将通过一个轶事来解释这一点:我曾经在一个非常大的代码库中工作，有一个方法定义在我们需要更新的`User`类上——特别是，我们需要将一个函数参数从`string`更改为`object`。

我做了更改，但是没有勇气提交更改——代码库中有太多对该函数的调用，以至于我不知道是否正确地更新了所有的实例。如果我错过了一些未经测试的助手文件中的调用怎么办？

知道的唯一方法是发布代码，并祈祷它不会因为错误而崩溃。

使用静态类型可以避免这种情况。如果我更新了一个函数，并且反过来更新了类型定义，类型检查器会在那里为我捕捉我错过的所有错误，这给了我保证和安心。我所要做的就是检查这些类型错误并修复它们。

#### 5。将数据与行为分开

静态类型的一个鲜为人知的好处是，它们有助于将数据与行为分开。

让我们用静态类型来重温一下我们的`calculateAreas`函数:

```
const calculateAreas = (radii: Array<number>): Array<number> => {
  var areas = [];
  for (var i = 0; i < radii.length; i++) {
    areas[i] = 3.14 * (radii[i] * radii[i]);
  }

  return areas;
}; 
```

Enter fullscreen mode Exit fullscreen mode

想想我们该如何构造这个函数。因为我们正在注释类型，所以我们被迫首先考虑我们打算使用的数据类型，以便我们可以适当地定义输入和输出类型。

[![js-code](img/8aaf51115559bd406934cb33ff7112ca.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WtKMZ9qd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/tN99fPc.png)

只有这样，我们才能实现逻辑:

[![more-js-code](img/cbbd799342ad98f5cf49efd4ab93d25e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--m8_HBP_6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/EE2mq2Q.png)

这种从行为中精确表达数据的能力允许我们明确我们的假设，并更准确地传达我们的意图，这减轻了一些精神负担，并给程序员带来一些精神上的清晰。没有它，我们只能以某种方式在精神上追踪它。

#### 6。消除一整类的错误

作为 JavaScript 开发人员，我们遇到的最常见的错误之一是运行时的类型错误。

例如，假设我们的初始应用程序状态被定义为:

```
var appState = {
  isFetching: false,
  messages: [],
}; 
```

Enter fullscreen mode Exit fullscreen mode

让我们假设我们然后进行一个 API 调用来获取消息，以便填充我们的`appState`。接下来，我们的应用程序有一个过于简化的视图组件，它将`messages`(在我们的状态中定义)作为一个道具，并将未读计数和每条消息显示为一个列表项:

```
import Message from './Message';

const MyComponent = ({ messages }) => {
  return (
    <div>
      <h1> You have { messages.length } unread messages </h1>
      { messages.map(message => <Message message={ message } /> )}
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果获取消息的 API 调用失败或返回`undefined`，我们将在生产中以类型错误结束:

`TypeError: Cannot read property ‘length' of undefined`

…你的程序崩溃了。你失去了一个顾客。真扫兴。

让我们看看类型如何帮助我们。我们将从向应用程序状态添加流类型开始。我将输入别名`AppState`，然后用它来定义状态:

```
type AppState = {
  isFetching: boolean,
  messages: ?Array<string>
};

var appState: AppState = {
  isFetching: false,
  messages: null,
}; 
```

Enter fullscreen mode Exit fullscreen mode

因为我们获取消息的 API 是不可靠的，这里我们说`messages`是字符串数组的`maybe`类型。

和上次一样——我们从不可靠的 API 获取消息，并在我们的视图组件中使用它:

```
import Message from './Message';

const MyComponent = ({ messages }) => {
  return (
    <div>
      <h1> You have { messages.length } unread messages </h1>
      { messages.map(message => <Message message={ message } /> )}
    </div>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

除了现在，心流会发现我们的错误并抱怨:

```
 <h1> You have {messages.length} unread  messages </h1>
                        ^^^^^^ property `length`. Property cannot be accessed on possibly null value                                                                  
<h1> You have {messages.length} unread messages </h1>
               ^^^^^^^^ null

<h1> You have {messages.length} unread  messages </h1>
                        ^^^^^^ property `length`. Property cannot be accessed on possibly undefined value

<h1> You have {messages.length} unread messages </h1>
               ^^^^^^^^ undefined

     { messages.map(message => <Message message={ message } /> )}
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ call of method `map`. Method cannot be called on possibly null value

     { messages.map(message => <Message message={ message } /> )}
       ^^^^^^^^ null

     { messages.map(message => <Message message={ message } /> )}
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ call of method `map`. Method cannot be called on possibly undefined value

     { messages.map(message => <Message message={ message } /> )}
       ^^^^^^^^ undefined 
```

Enter fullscreen mode Exit fullscreen mode

哇伙计！

因为我们将`messages`定义为`maybe`类型，所以我们说它可以是`null`或`undefined`。但是它仍然不允许我们在没有进行`null`检查的情况下对其执行操作(如`.length`或`.map`),因为如果`messages`值实际上是`null`或`undefined`，如果我们对其执行任何操作，我们将会以类型错误结束。

所以让我们回过头来，将我们的视图函数更新为类似于:

```
 const MyComponent = ({ messages, isFetching }: AppState) => {
  if (isFetching) {
    return <div> Loading... </div>
  } else if (messages === null || messages === undefined) {
    return <div> Failed to load messages. Try again. </div>
  } else {
    return (
      <div>
        <h1> You have { messages.length } unread messages </h1>
        { messages.map(message => <Message message={ message } /> )}
      </div>
    );
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

Flow 现在知道我们已经处理了消息是`null`或`undefined`的情况，因此代码类型检查没有错误。长期停滞运行时类型错误:)

#### 7。减少单元测试的数量

我们前面看到了静态类型如何帮助消除复杂的错误处理，因为它们保证了输入和输出类型。因此，它们也减少了单元测试的数量。

例如，让我们回到带有错误处理的动态类型的`calculateAreas`函数:

```
const calculateAreas = (radii) => {
  // Handle undefined or null input
  if (!radii) {
    throw new Error("Argument is missing");
  }

  // Handle non-array inputs
  if (!Array.isArray(radii)) {
    throw new Error("Argument must be an array");
  }

  var areas = [];

  for (var i = 0; i < radii.length; i++) {
    if (typeof radii[i] !== "number") {
      throw new Error("Array must contain valid numbers only");
    } else {
      areas[i] = 3.14 * (radii[i] * radii[i]);
    }
  }

  return areas;
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们是勤奋的程序员，我们可能会想到测试无效输入，以确保它们在我们的程序中得到正确处理:

```
it('should not work - case 1', () => {
  expect(() => calculateAreas([null, 1.2])).to.throw(Error);
});

it('should not work - case 2', () => {
  expect(() => calculateAreas(undefined).to.throw(Error);
});

it('should not work - case 2', () => {
  expect(() => calculateAreas('hello')).to.throw(Error);
}); 
```

Enter fullscreen mode Exit fullscreen mode

…等等。除了我们很可能忘记测试一些边缘案例——那么我们的客户就是发现问题的人。:(

因为测试仅仅基于我们认为要测试的案例，所以它们是存在的，并且容易规避。

另一方面，当我们需要定义类型时:

```
const calculateAreas = (radii: Array<number>): Array<number> => {
  var areas = [];
  for (var i = 0; i < radii.length; i++) {
    areas[i] = 3.14 * (radii[i] * radii[i]);
  }

  return areas;
}; 
```

Enter fullscreen mode Exit fullscreen mode

…我们不仅保证了我们的意图与现实相符，而且它们也更难逃脱。与基于经验的测试不同，测试类型是通用的，很难含糊其辞。

这里的大背景是:测试擅长测试逻辑，类型擅长测试数据类型。结合起来，部分之和大于整体。

#### 8。领域建模工具

我最喜欢的类型用例之一是领域建模。领域模型是一个领域的概念模型，包括数据和数据上的行为。理解如何使用类型进行领域建模的最好方法是看一个例子。

假设我有一个应用程序，其中用户有一种或多种支付方式在平台上购物。他们可以使用三种支付方式(Paypal、信用卡、银行账户)。

所以我们先键入这三种不同的支付方式的别名:

```
type Paypal = { id: number, type: 'Paypal' };
type CreditCard = { id: number, type: 'CreditCard' };
type Bank = { id: number, type: 'Bank' }; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以将我们的`PaymentMethod`类型定义为一个不相交的并集，有三种情况:

```
type PaymentMethod = Paypal | CreditCard | Bank; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，让我们为我们的应用程序状态建模。为了简单起见，让我们假设我们的应用程序数据只包含用户的支付方式。

```
type Model = {
  paymentMethods: Array<PaymentMethod>
}; 
```

Enter fullscreen mode Exit fullscreen mode

这样够好了吗？我们知道，要获得用户的支付方式，我们需要发出一个 API 请求，根据我们在获取过程中的位置，我们的应用程序将有不同的状态。所以实际上有四种可能的状态:

1)我们尚未提取付款方式

2)我们正在提取付款方式

3)我们已成功提取付款方式

4)我们尝试提取，但提取付款方式时出现错误

但是我们简单的带有`paymentMethods`的`Model`类型并没有涵盖所有这些情况。相反，它假设`paymentMethods`一直存在。

嗯。有没有一种方法可以将我们的应用程序状态建模为这四种情况之一，并且只有这四种情况？我们来看看:

```
type AppState<E, D>
  = { type: 'NotFetched' }
  | { type: 'Fetching' }
  | { type: 'Failure', error: E }
  | { type: 'Success', paymentMethods: Array<D> }; 
```

Enter fullscreen mode Exit fullscreen mode

我们使用分离联合类型将我们的状态定义为上述四种场景之一。请注意我是如何使用一个`type`属性来确定我们的应用程序处于四种状态中的哪一种。这个`type`属性实际上是使它成为一个不相交的并集的原因。利用这一点，我们可以进行案例分析，以确定何时有支付方式，何时没有。

您还会注意到，我将一个泛型类型`E`和`D`传入应用程序状态。类型`D`将代表用户的支付方式(上面定义的`PaymentMethod`)。我们还没有定义类型`E`，这将是我们的错误类型，所以让我们现在:

```
type HttpError = { id: string, message: string }; 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以将我们的应用领域建模为:

```
type Model = AppState<HttpError, PaymentMethod>; 
```

Enter fullscreen mode Exit fullscreen mode

总之，我们的应用程序状态的签名现在是`AppState<E, D>`——其中`E`的形状是`HttpError`，而`D`是`PaymentMethod`。而`AppState`有四种(也只有这四种)可能的状态:`NotFetched`、`Fetching`、`Failure`和`Success`。

[![js-output](img/57f272e233f4f37820661f55cc15e90f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--P_NxAd77--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i.imgur.com/EzFPrmp.png)

我发现这种类型的领域建模对于思考和构建针对特定业务规则的用户界面非常有用。业务规则告诉我们，我们的应用程序只能处于其中一种状态。因此，这允许我们显式地表示构建我们的应用程序状态，并保证它只会处于预定义的状态之一。当我们构建这个模型时(例如，创建一个视图组件)，很明显我们需要处理所有四种可能的状态。

此外，代码变得自文档化——您可以查看联合案例并立即弄清楚应用程序状态是如何构造的。