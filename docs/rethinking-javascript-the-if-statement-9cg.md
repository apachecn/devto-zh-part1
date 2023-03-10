# 重新思考 JavaScript:if 语句

> 原文：<https://dev.to/joelnet/rethinking-javascript-the-if-statement-9cg>

功能性思维打开了我对编程的思维。它让我对代码有了更深的理解。这也让我开始质疑这门语言的许多核心特性。

我最近一直在质疑`if`声明本身。

在编写了一个完全没有一条`if`语句的应用程序之后，我发现`if`是可选的。

如果你不熟悉函数式编程，这可能听起来像是一个疯子的咆哮，但是请不要离开我，让我解释一下。

> 学习多种方法来解决同一个问题会极大地增强你的思维能力。

首先，满足`if`语句替换，三元运算符:

```
condition ? expr1 : expr2 
```

Enter fullscreen mode Exit fullscreen mode

函数式编程教过我这样写小函数:(不要吼我不谄媚，那是另一篇文章)

```
const add = (x, y) => x + y 
```

Enter fullscreen mode Exit fullscreen mode

`if`语句不适合这种类型的函数，但是三元运算符适合！

```
const isGreaterThan5 = x => x > 5 ? 'Yep' : 'Nope' 
```

Enter fullscreen mode Exit fullscreen mode

很快，我开始发现几乎每一个`if`语句的实例都可以用等价的三元运算来代替。

```
// typical code you might stumble upon
function saveCustomer(customer) {
  if (isCustomerValid(customer)) {
    database.save(customer)
  } else {
    alert('customer is invalid')
  }
}

// ternary equivalent
function saveCustomer(customer) {
  return isCustomerValid(customer)
    ? database.save(customer)
    : alert('customer is invalid')
}

// ES6 style
const saveCustomer = customer =>
  isCustomerValid(customer)
    ? database.save(customer)
    : alert('customer is invalid') 
```

Enter fullscreen mode Exit fullscreen mode

然后我开始思考三元运算符如何融入一个`else-if`语句。我用嵌套的三元组做到了这一点，但是它真的很乱，不容易理解。

```
// old school else-if
function customerValidation(customer) {
  if (!customer.email) {
    return error('email is require')
  } else if (!customer.login) {
    return error('login is required')
  } else if (!customer.name) {
    return error('name is required')
  } else {
    return customer
  }
}

// ES6 style custom formatted ternary magic
const customerValidation = customer =>
  !customer.email   ? error('email is required')
  : !customer.login ? error('login is required')
  : !customer.name  ? error('name is required')
                    : customer 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以清楚地看到左侧定义的所有条件和右侧返回的值。

尽管可以用三元运算符替换每一个`if`语句，但我并不是说你应该这样做。这只是你使用和考虑的另一个工具。

# 但是为什么呢？

我们不仅仅是消除了`if`，而且还消除了语句和块以支持表达式。虽然这样做没有什么“功能性”。学习不使用语句或块进行编码是允许您编写更多功能代码的多个前置功能步骤之一。

# 关于可读性的说明

“可读性”和“曝光度”是有直接关联的。

这方面的一个例子是第一次看到一个新词，如“背信弃义”。由于从未见过，你可能不得不阅读每一封信，并在脑海中读出它。你也可能更喜欢用它的同义词“不忠”，你觉得这样更“容易”。毕竟，‘不忠诚’这个词是伴随你长大的，也是你一生都在使用的。

但是当你把这个词读出来几次，知道它的定义，并开始在几个句子中使用它，它就会开始变成你的第二天性。你现在可以快速浏览单词“背信弃义”,暂停时间不会比你阅读其他单词的时间更长，因为它变得越来越熟悉。

现在你的词汇量增加了，你可以更有效地交流了，因为你有更多的工具可以从你的语言工具箱里拿出来。

这个三元组的结构也模仿了 Ramda 的 [cond](http://ramdajs.com/docs/#cond) 的结构，它也有左边的条件和右边的动作，所以使用其中一个应该会让你更熟悉另一个。

```
var fn = R.cond([
  [R.equals(0),   R.always('water freezes at 0°C')],
  [R.equals(100), R.always('water boils at 100°C')],
  [R.T,           temp => 'nothing special happens at ' + temp + '°C']
]);
fn(0); //=> 'water freezes at 0°C'
fn(50); //=> 'nothing special happens at 50°C'
fn(100); //=> 'water boils at 100°C' 
```

Enter fullscreen mode Exit fullscreen mode

[甚至连 Mozilla 都建议](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator):

> 通过将三元运算符分成多行并利用额外的空白，三元运算符可以非常干净地替换一长串 if/else 语句。这为表达相同的逻辑提供了一种语法上简单的方式:

我保证，可读性会随着时间的推移而提高。

# 结束

这篇重新思考 JavaScript 的文章只是系列文章中的一篇。不久的将来还会有更多！

我很想听到你的反馈和想法，关于你如何重新思考 if 声明。你有没有不确定这将如何工作的场景？告诉我，让我知道！

**继续第 2 部分重新思考 JavaScript:[For 循环的死亡](https://medium.com/@joelthoms/rethinking-javascript-death-of-the-for-loop-c431564c84a8)。**

我知道这是一件小事，但当我在这里和 Twitter ( [@joelnet](https://twitter.com/joelnet) )上收到这些关注通知时，我感到非常高兴。或者你觉得我满嘴屁话，在下面的评论里告诉我。
干杯！

原贴于此:[https://hacker noon . com/refhinking-JavaScript-the-if-statement-b 158 a 61 CD 6 CB](https://hackernoon.com/rethinking-javascript-the-if-statement-b158a61cd6cb)