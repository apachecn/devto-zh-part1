# 编写现代 JavaScript 代码

> 原文：<https://dev.to/scastiel/writing-modern-javascript-code>

还记得 JavaScript 是一种当光标在页面上时用来改变元素的语言吗？这些日子已经过去了，每种语言都会随着时间的推移而演变，我们使用语言的方式也是如此。看看你一两年前写的代码:你觉得丢人吗？如果是的话，这篇文章是写给你ðÿ™‚的

我将在这里尝试列出一些好的实践，让您的 JavaScript 代码更容易编写、阅读和维护。

## 使用可以格式化你的代码的 linter

我给你的第一个建议是使用 linter，它将检查你是否遵守一些规则，这些规则使你的代码从一个文件到另一个文件保持一致，特别是如果你是几个开发人员在同一个项目上工作:缩进，括号中的空格，用`===`代替`==`…

但更重要的是，尽可能让你的 linter 自动修复你的代码。 [ESLint](http://eslint.org/) 很好地做到了这一点(通过`--fix`选项)，它与所有主要的 ide 很好地集成，可以在保存时自动修复文件。

你也可以使用比林挺更注重格式的[beautiful](https://github.com/prettier/prettier)，但结果基本上是相同的ðÿ˜‰

下一点将帮助你选择使用什么样的规则:

## 使用现代的棉绒规则

如果你想知道你的代码需要什么规则，这里有一个提示: [StandardJS](https://standardjs.com/) 。这是一个**非常**严格的 linter，在规则中不会给你任何选择，但是他们中的每一个都越来越被社区所承认。以下是一些例子:

*   使用 2 个空格进行缩进(我曾经使用 4 个空格，但实际上使用 2 个也很好)
*   没有分号(刚开始很诡异，但是几天后就回不去了)
*   关键字后的空格(如`if`)在花括号中，而不是在括号内
*   还有[多了很多](https://standardjs.com/rules.html)。

StandardJS 是一个独立的节点模块，可以 lint 并修复您的代码，但是如果您想在一个大型的现有项目中使用它并停用一些规则(因为有些规则需要大量修改)，您也可以使用 [ESLint 预定义配置](https://github.com/feross/eslint-config-standard)。例如，我停用了规则 [no-mixed-operators](http://eslint.org/docs/rules/no-mixed-operators) 和[import/no-web pack-loader-语法](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/no-webpack-loader-syntax.md)。

## 使用 ES2015+新功能

如果你用 JavaScript 开发，你不可能没有听说过 ES2015+(或者 ES6、ES7……)的特性。以下是我再也离不开的东西:

*   箭头函数:编写类似`x => x * 2`的函数对于函数式编程非常有用(见下一点)
*   类:停止使用原型函数，类是非常酷的ðÿ˜‰(但是不要滥用，JavaScript 比任何面向对象的语言都好)
*   数组和对象的操作:

```
function doSomething() {
  const a = doSomethingElse()
  const b = doSomethingWithA(a)
  const otherResults = { c: 'ðŸ˜º', d: 'ðŸ¶' }
  return { a, b, ...otherResults } // equivalent to { a: a, b: b }
}
const { a, c, ...rest } = doSomething() // Also works with arrays!
// `rest` looks like { b: ..., d: 'ðŸ¶' } 
```

Enter fullscreen mode Exit fullscreen mode

*   使用`async/await`让承诺更容易实现:

```
// Please try to write the same code with classic promises ;)
async function doSomething() {
  const a = await getValueForA()
  const b = await getValueForBFromA(a)
  const [c, d] = await Promise.all([
    // parallel execution
    getValueForC(), getValueForDFromB(b)
  ])
  const total = await calculateTotal(a, b, c, d)
  return total / 1000
} 
```

Enter fullscreen mode Exit fullscreen mode

想知道如何使用这些神奇的功能？[我的一篇文章给你一些建议](https://blog.castiel.me/posts/002-use-the-coolest-es6-features-everywhere.html)！(顺便说一句，有了 Node.js 的最新版本，你可能不再需要 Babel 来使用ðÿ˜€最伟大的新特性)

## 使用功能编程

现在非常炒作，函数式编程最近获得了很大的成功，不仅仅是在 JavaScript 中。原因？它使代码更可预测、更安全、更有确定性，并且当您习惯于它时更容易维护。以下是一些简单的建议:

首先停止使用*进行*循环。在大多数(每？)以防你不需要它们。例如:

```
const arr = [{ name: 'first', value: 13 }, { name: 'second', value: 7 }]

// Instead of:
const res = {}
for (let i = 0; i < arr.length; i++) {
  const calculatedValue = arr[i].value * 10
  if (calculatedValue > 100) {
    res[arr[i].name] = calculatedValue
  }
}

// Prefer:
const res = arr
  .map(elem => ({ name: elem.name, calculatedValue: elem.value * 10 }))
  .filter(elem => elem.calculatedValue > 100)
  .reduce((acc, elem) => ({
    [elem.name]: calculatedValue,
    ...acc
  }), {}) 
```

Enter fullscreen mode Exit fullscreen mode

好吧，我承认这是一个非常极端的例子，如果你不习惯函数式编程，它可能看起来更复杂。也许我们可以简化一下:

```
const enrichElementWithCalculatedValue =
  elem => ({ name: elem.name, calculatedValue: elem.value * 10 })
const filterElementsByValue = value =>
  elem => elem.calculatedValue > value
const aggregateElementInObject = (acc, elem) => ({
  [elem.name]: calculatedValue,
  ...acc
})
const res = arr
  .map(enrichElementWithCalculatedValue)
  .filter(filterElementsByValue(100))
  .reduce(aggregateElementInObject, {}) 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们定义了三个函数，基本上正如它们的名字所表达的那样。第二个建议:创建局部函数(甚至在现有的函数中)来记录你的代码，而不加注释！

注意，这三个局部函数不修改它们执行时的上下文。没有外部变量被修改，没有其他服务被调用…在函数式编程中，它们被称为*纯函数*。他们有一些很大的优势:

*   它们很容易测试，因为根据给定的参数，只有一个可能的结果，即使我们多次调用这个函数；
*   无论应用程序的实际状态如何，它们都保证给出相同的结果；
*   应用程序状态在函数调用前后保持不变。

所以我的第三个建议是:大量使用纯函数！

## 其他一些建议完成

*   习惯使用异步代码，多使用承诺，用 [RxJS](http://reactivex.io/rxjs/) 看看 observales(有[一个关于函数式编程的很棒的教程，它会导致反应式编程](http://reactivex.io/learnrx/)
*   写测试！应该是显而易见的，但我知道很多项目都有未经测试的代码，尽管测试 JavaScript(前端或后端)并不像看起来那么困难。
*   使用语言的最新特性:例如，停止写`arr.indexOf(elem) !== -1`而支持`arr.includes(elem)`。
*   阅读大量技术文章: [JavaScript subreddit](https://www.reddit.com/r/javascript/) 是了解生态系统中最酷实践的一个非常好的来源。

最后，我能给你的最好的建议是:**永远重构你的代码！**改进你一年前写的一个模块？抓住机会用`const`替换`var`，使用箭头函数或`async/await`来简化代码……像ðÿ˜‰一样编写代码总是更好

*这篇文章最初发表在我的博客上。*