# 为什么在 JavaScript 中使用静态类型？(第三部分)

> 原文：<https://dev.to/iampeekay/why-use-static-types-in-javascript-part-3>

## **使用静态类型的缺点**

像生活和编程中的其他事情一样，静态类型检查也有它的缺点。

重要的是我们要理解和承认它们，这样我们才能做出明智的决定，什么时候静态类型有意义，什么时候静态类型不值得。

以下是其中的一些考虑因素:

#### 1。静态类型需要前期投资来学习

对于初学者来说，JavaScript 是一种如此神奇的语言，原因之一是它不需要学生在使用这种语言之前学习整个类型系统。

当我最初学习 Elm(一种静态类型的函数式语言)时，类型经常碍事。我会经常遇到与我的类型定义相关的编译器错误。

学习如何有效地使用类型系统已经是学习语言本身的一半了。因此，静态类型使得 Elm 的学习曲线比 JavaScript 更陡峭。

这对初学者来说尤其重要，因为他们学习语法的认知负荷一直很高。向组合中添加类型会让初学者不知所措。

#### 2。冗长会让你陷入困境

静态类型经常使程序看起来更加冗长和混乱。

例如，代替:

```
async function amountExceedsPurchaseLimit(amount, getPurchaseLimit){
  var limit = await getPurchaseLimit();

  return limit > amount;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们必须写:

```
async function amountExceedsPurchaseLimit(
  amount: number,
  getPurchaseLimit: () => Promise<number>
): Promise<boolean> {
  var limit = await getPurchaseLimit();

  return limit > amount;
} 
```

Enter fullscreen mode Exit fullscreen mode

而不是:

```
var user = {
  id: 123456,
  name: 'Preethi',
  city: 'San Francisco',
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们必须写:

```
type User = {
  id: number,
  name: string,
  city: string,
};

var user: User = {
  id: 123456,
  name: 'Preethi',
  city: 'San Francisco',
}; 
```

Enter fullscreen mode Exit fullscreen mode

显然，这会增加额外的代码行。但是有几个论点反对这是一个真正的负面影响。

首先，正如我们前面提到的，静态类型有助于消除整个测试类别。一些开发人员会认为这是一个完全合理的权衡。

其次，正如我们前面看到的，静态类型有时可以消除复杂的错误处理，从而大大减少代码的视觉混乱。

很难说冗长是否是反对类型的真正理由，但这是一个值得记住的理由。

#### 3。类型需要时间来掌握

学习如何在程序中最好地指定类型需要时间和大量的练习。此外，对于什么是值得静态跟踪的，什么是保持动态的，培养良好的判断力也需要仔细的思考、实践和经验。

例如，我们可能采取的一种方法是用类型对关键的业务逻辑进行编码，同时让短暂的或不重要的逻辑保持动态，以避免不必要的复杂性。

这种区别可能很难区分，尤其是当对类型缺乏经验的开发人员正在匆忙做出判断的时候。

#### 4。静态类型会阻碍快速开发

正如我前面提到的，在我学习 Elm 的时候，类型让我犯了一点错误——特别是在添加代码或者进行修改的时候。经常被编译器错误分心让我很难感觉到自己有任何进步。

这里的论点是，静态类型检查可能会导致程序员过于频繁地失去注意力——正如我们所知，注意力是编写好程序的关键。

不仅如此，静态类型检查器并不总是完美的。有时，您会遇到这样的情况，您知道您需要做什么，但是类型检查正好碍事。

我确信我还遗漏了其他的权衡，但这些对我来说是最重要的。

### 接下来，第四部分(即将推出)

在最后一节，我们将讨论使用静态类型是否有意义以及何时有意义。敬请期待！