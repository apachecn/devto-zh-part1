# 这种更少的 Javascript 开发

> 原文：<https://dev.to/theodesp/this-less-javascript-development>

*点击阅读原文

Javascript 中的关键字 this 可能是 JavaScript 中讨论最多的关键字，因为它根据调用位置和启用的环境模式(严格与非严格)有不同的行为。

**提示**:如果你想了解更多关于关键词“this”的工作原理，你可以阅读这些优秀的文章:

[给初学者的关键词](https://codeburst.io/javascript-the-keyword-this-for-beginners-fb5238d99f85)

[Javascript 中“this”的简单规则](https://codeburst.io/the-simple-rules-to-this-in-javascript-35d97f31bde3)

我不会介绍它是如何工作的，而是将重点放在一个不同的想法和编程方法上。

这种行为方式让一些程序员很生气，所以他们决定根本不使用它。我不怪他们，因为为了不处理它的特性(参见 arrow functions 等，这个 binding 等),它已经产生了如此多的副作用和语言扩展，以至于最终我们可以不使用它。所以，现在让我们通过深入思考，尝试一种新的方式来应对这个“没有这个”的世界…

...

## 想法

我们需要记住的主要一点是，函数是 Javascript 中的一等公民。您可以将它们用作函数参数的值，也可以返回函数。当你这样做的时候，你就创造了一个终结。**闭包**是一个内部函数，它可以访问外部(封闭)函数的变量作用域链。这些变量是函数的私有变量，所以保存内部函数值的作用域不能直接访问它们。比如看看这个函数加法器:

```
function makeAdder(base) {
  let current = base;
  return function(addition) {
    current += addition;
    return current;    
  }
} 
```

这个函数将接受一个基本参数，并返回一个函数。该函数将接受一个参数，当被调用时，它将访问当前变量，因此它将执行加法并返回结果。对于每个后续调用，它每次都会保留当前值。

因此，需要注意的重要一点是:

闭包定义了它们自己的局部词法环境，作为函数的私有空间。

闭包是 Javascript 中一个非常强大的特性，如果使用正确，可以构建非常好的抽象。

现在我们有了这些知识，我们可以扩展返回一个知道如何在本地环境中操作的特定对象的概念。可以把它看作闭包公开的公共 API。它的名字是**显示模块模式**。

你可以说，在显示模块模式中，你可以定义哪些成员是公共可访问的，哪些成员是私有的。这使得代码可读性更好，也更容易使用。

这里有一个例子:

```
let counter = (function() {
  let privateCounter = 0;
  function changeBy(val) {
    privateCounter += val;
  }
  return {
    increment: function() {
      changeBy(1);
    },
    decrement: function() {
      changeBy(-1);
    },
    value: function() {
      return privateCounter;
    }
  };   
})();
counter.increment();
counter.increment();
console.log(counter.value()); // logs 2 
```

如您所见，privateCounter 变量是我们需要处理的数据，操作包括递增、递减和值

有了这些知识，我们就有了编程所需的所有工具，而不需要这些，我将用一个例子来证明这一点。

## 举个例子

为了演示一个如何使用闭包和函数的简单例子，我们将实现一个简单的数据结构，称为 **deque** 。双端队列是一种抽象的数据类型，它可以作为一个队列，元素可以添加到队列的前部(头部)或后部(尾部),也经常被称为头尾链表，因为我们将使用链表来实现。这听起来可能很复杂，但实际上并不复杂，如果您遵循逻辑，您将能够理解如何实现 deque 必须满足的所有必需操作。请注意，虽然这只是一个示例，但是您可以将类似的技术应用于任何保存状态的对象，无论是模型对象、商店还是其他对象。

下面是必需的操作。

```
create: Creates a new deque object.
isEmpty: Checks if the deque object is empty.
pushBack: Appends a new item at the end of the deque.
pushFront: Appends a new item at the front of the deque.
popBack: Removes and returns the last element of the deque.
popFront: Removes and returns the first element of the deque. 
```

让我们首先考虑如何用对象和闭包变量来表示我们的 deque。如果我们有一个很好的表示，我们可以很容易地实现操作。

我们需要一个变量，姑且称之为`data`，它将保存队列中的每一项。我们还需要 deque 上第一项和最后一项的指针。让我们分别称他们为`head`和`tail`。由于它是一个链表实现，我们需要一种方法来将每个条目相互链接，因此对于每个条目，我们需要指向下一个和上一个条目的指针。让我们称他们为`next`和`prev`。最后，我们需要记录我们有多少物品，所以我们称之为`length`。

接下来，我们需要将变量分组到正确的位置。对于每个节点，我们需要数据和指向 next 和 prev 的指针，所以让我们将节点定义为:

```
let Node = {
  next: null,
  prev: null,
  data: null
}; 
```

对于每个队列，我们需要一个头、一个尾和一个长度，所以让我们将队列定义为:

```
let Deque = {
  head: null,
  tail: null,
  length: 0
}; 
```

现在，这些对象表示一个节点和一个队列的规范。我们需要把它们放在我们的闭包里:

```
module.exports = LinkedListDeque = (function() {
  let Node = {
    next: null,
    prev: null,
    data: null
  };
  let Deque = {
    head: null,
    tail: null,
    length: 0
  };
 // need to return the public api here
})(); 
```

现在我们已经建立了闭包变量，我们可以实现 create 方法，它很简单:

```
function create() {
  return Object.create(Deque);
} 
```

就是这样。新构造的 deque 没有元素。现在是 isEmptymethod。

```
function isEmpty(deque) {
  return deque.length === 0
} 
```

我们传递一个 deque 对象，并检查它的长度属性是否为零。酷毙了。

现在，对于 pushFront 方法，我们必须实际执行以下步骤:

*   创建一个新节点。
*   如果队列是空的，你只需要分配头部和尾部来指向新的节点。否则，如果 dequee 不为空，则获取 dequee 的当前头，并确保将 prev 赋给新节点，并让新节点将其指向头的旁边。因此，第一项将是新节点，后面是旧头。我们不需要忘记更新 deque head 以指向新的节点。
*   增加长度。

```
function pushFront(deque, item) {
  // Create a new Node
  const newNode = Object.create(Node);
  newNode.data = item;

  // Grab the current head
  let oldHead = deque.head;
  deque.head = newNode;
  if (oldHead) {
    // We have at least one item. Prepend new Node in the front
    oldHead.prev = newNode;
    newNode.next = oldHead;
  } else {// The deque is empty. Just assign tail.
    deque.tail = newNode;
  }
  // Update length
  deque.length += 1;

  return deque;
} 
```

类似地，我们对队列的最后一项的 pushBack 方法做同样的事情。

```
function pushBack(deque, item) {
  // Create a new Node
  const newNode = Object.create(Node);
  newNode.data = item;

  // Grab the current tail
  let oldTail = deque.tail;
  deque.tail = newNode;
  if (oldTail) {
    // We have at least one item. Append new Node at the back
    oldTail.next = newNode;
    newNode.prev = oldTail;
  } else {// The deque is empty. Just assign head.
    deque.head = newNode;
  }
  // Update length
  deque.length += 1;

  return deque;
} 
```

最后，我们需要在闭包外部公开我们的公共方法，因此我们返回一个导出的必需方法的对象:

```
return {
 create: create,
 isEmpty: isEmpty,
 pushFront: pushFront,
 pushBack: pushBack,
 popFront: popFront,
 popBack: popBack
} 
```

到目前为止一切顺利。那么我们如何使用它呢？让我们用一个简单的例子来展示这个结构:

```
const LinkedListDeque = require('./lib/deque');
d = LinkedListDeque.create();
LinkedListDeque.pushFront(d, '1'); // [1]
LinkedListDeque.popFront(d); // []
LinkedListDeque.pushFront(d, '2'); // [2]
LinkedListDeque.pushFront(d, '3'); // [3]<=>[2]
LinkedListDeque.pushBack(d, '4'); // [3]<=>[2]<=>[4]
LinkedListDeque.isEmpty(d); // false 
```

请注意我们是如何将数据和针对数据的操作明确分离的。只要我们持有一个队列的有效引用，我们就可以放心地使用特定的 LinkedList 操作。

## 作业时间到了

是的，你认为你不练习任何东西就可以离开，是吗？为了完全理解事物是如何工作的，并且你能感受到这种类型的开发，我建议你自己做下面的练习。在这里克隆/派生我的 github repo [并开始工作。(不，你在那里找不到任何答案。)](https://github.com/theodesp/thisless-deque)

1.  基于前面的操作，实现满足了剩下的部分。实现`popBack`和`popFront`函数，相应地移除并返回队列中的第一项和最后一项。

2.  这个特殊的 deque 实现使用了链表。一个不同的实现使用普通的 Javascript 数组对象。使用一个数组实现一个队列所需的所有操作，并将其命名为`ArrayDeque`。记住没有这个，就没有新的。

3.  对于`ArrayDeque`和`LinkedListDeque`的每一个方法，你能发现在时间和空间复杂度上有什么不同。在 markdown 中写下你的想法。
    实现队列的另一种方式是同时使用数组和 LinkedList。称它为`MixedDeque`。在这种情况下，你分配一个固定大小的数组块，比如说 64 个条目。当你在块中推入太多超过块大小的条目时，你分配一个新的块，你使用一个链表以 FIFO 的方式将块链接在一起。使用这种方法实现 deque 所需的方法。这种结构的优点和缺点是什么？在 markdown 中写下你的想法。

4.  作者 Addy Osmany 在他的关于 Javascript 中的[设计模式的书中指出，揭示模块模式的一个缺点是，如果私有函数引用公共函数，那么如果需要补丁，公共函数不能被覆盖，因为私有函数将继续引用私有实现。因此，这种模式不适用于公共成员，只适用于函数。想办法克服这个缺点。想想依赖关系是什么，以及如何逆转这种控制。如何确保所有的私有函数访问公共函数时，我们总是能控制闭包内部传递的内容，从而不必担心公共函数上的补丁？在 markdown 中写下你的想法。](https://addyosmani.com/resources/essentialjsdesignpatterns/book/)

5.  编写一个名为`join`的方法，将两个 deques 粘合在一起。例如，调用`LinkedListDeque.join(first, second)`将在第一个队列的末尾追加第二个队列，并返回新的队列。

6.  为 deque 开发一个非破坏性的遍历机制，以便它可以在 for 循环中迭代。对于这个例子，您可以使用 es6 迭代器。

7.  为 deque 开发一个非破坏性的反向遍历机制，以便它可以在 for 循环中以相反的顺序迭代。

8.  加入这个运动，在 Github 上发布你的代码，吹嘘你做了一个没有这个的 dequee 实现，并且你对 deques 了如指掌。别忘了提到你的[牛逼导师](https://www.linkedin.com/in/theofanis-despoudis-7bb30913/)。

9.  **额外标记**:使用任何一种测试框架来为你的所有实现添加测试。不要忘记测试边缘案例。

10.  **额外标记**:修改 deque 实现以允许处理加权节点。例如，当你推回一个项目时，你可以指定一个`weight`或者一个优先级`score`。如果不指定权重，其行为与普通的队列相同。如果你指定一个权重，你需要确保在每次操作之后，列表中的最后一个元素具有最低的优先级，而列表中的第一个元素具有最高的优先级。添加测试来验证该不变量。

11.  **额外标记**:一个**多项式**是一个表达式，可以写成:`an * x^n + an-1*x^n-1 + ... + a1x^1 + a0`其中`an..a0`是系数，`n…1`是指数。设计一个可以表示多项式的 deque 类。加法多项式的加、减、乘、除方法。假设你只传递简化的多项式，而不是复杂的多项式。添加测试以验证结果是否正常。确保在您的所有方法中，您都将结果作为新的 deque 返回。

12.  **Ultra Marks** :挑一个 Javascript 以外的编程语言(Python，Go，C++等。)并实施之前的所有练习。哇哦。

## 结论

好吧，我希望你做了功课，并从中学到了一些东西。如果你认为不使用它的好处是值得的，你也可以看看这个 eslint 规则插件，在那里你可以把它应用到你的 lint 检查中。确保你先和你的同事达成共识，因为无论何时他们看到你，你都可能会变得暴躁。

**如果这篇文章有帮助，请分享并关注我的其他[文章](https://medium.com/@fanisdespoudis/)。你可以在 [GitHub](https://github.com/theodesp) 和 [LinkedIn](https://www.linkedin.com/in/theofanis-despoudis-7bb30913/) 上关注我。如果你有任何想法和改进，请随时与我分享。**

快乐编码。