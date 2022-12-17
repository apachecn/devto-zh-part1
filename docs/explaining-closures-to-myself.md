# 向自己解释闭包

> 原文：<https://dev.to/damcosset/explaining-closures-to-myself>

## 简介

我写作是因为它帮助我记住信息。无论我在学习什么科目，我都强迫自己用语言表达出来，就像我在教别人一样。我的主要目的不是教别人，而是教我自己。我们总是认为我们了解一些事情，直到我们不得不解释它。知道的人做，教的人做的更好。在本文中，我将尝试自学闭包。

## 关闭

闭包定义如下:

> 一个闭包就是当一个函数能够记住并访问它的词法范围时，即使这个函数是在它的词法范围之外执行的。

### 范围

要理解闭包，我必须首先理解作用域。程序中的作用域是在某个位置存储变量并在以后检索它们的一组规则。程序中的某些结构创建自己的作用域(函数、if、for 循环...).如果我在一个作用域中声明了一个变量，它在另一个作用域中是不可访问的。

```
// I am in the global scope
const a = 'Damien'

if( true ) {
  // This is a different scope
  const a = 'John'
  console.log(a) //John
}

const func = () => {
  // This is a third scope
  const a = 'Joe'
  console.log(a) // Joe
}

func()
console.log(a) // Damien 
```

Enter fullscreen mode Exit fullscreen mode

如果您试图检索一个当前范围内不存在的变量，Javascript 将在外部范围内寻找它。Javascript 将重复这个过程，直到不再有外部范围需要检查。如果变量没有找到，你会得到一个 ReferenceError:

```
// I am in the global scope

if( true ) {
  // This is a different scope
  const a = 'John'
  console.log(a) //John
}

const func = () => {
  // This is a third scope
  const a = 'Joe'
  console.log(a) // Joe
}

console.log(a) // ReferenceError
func() 
```

Enter fullscreen mode Exit fullscreen mode

我移除了全局范围内的变量声明。当我试图检索它时，Javascript 找不到它并返回一个错误。

```
// I am in the global scope
const a = 'Damien'

if( true ) {
  // This is a different scope
  console.log(a) //Damien
}

const func = () => {
  // This is a third scope
  const a = 'Joe'
  console.log(a) // Joe
}

console.log(a) // Damien
func() 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我删除了 if 块中的变量声明。Javascript 在这个范围内找不到变量 a，所以在外部范围内查找。程序在这个外部作用域(全局作用域)中找到一个= 'Damien '并使用它。

## 回封

现在，我对示波器有了更多的了解。当函数在其作用域之外执行时，闭包允许函数访问其作用域。让我们来看看实际情况。

```
function outer(){
  const a = 'Damien'

  function inner(){
    console.log(a)
  }

  return inner
}
const func = outer()

func() // 'Damien' 
```

Enter fullscreen mode Exit fullscreen mode

为什么这是一个终结？作为一个闭包，这意味着函数 *inner* 正在其词法范围之外执行，并且仍然可以访问其作用域。那么这里发生了什么？函数*外部*返回对*内部*函数的引用。我们执行*外部*函数，并将其传递给 func 变量。然后我们通过调用 *func()* 来执行*内部*函数。*内部*被执行，但在其声明的词法范围之外。它在*外部*函数之外执行。理论上，程序会释放空间，并看到不再需要我们的*外部*函数(垃圾收集器)。

*inner* 在 *outer* 的内部范围上有一个词法范围闭包。这保持了*内部*使用的范围。*内*在*外*作用域中拥有的引用使该作用域保持活动状态。== >关闭。

## 更多例子？

好的，还是有点模糊。你能给我举更多的例子吗？也许是现实世界的？

```
function chrono( message ){
  setInterval( function timer() {
    console.log( message )
  }, 1000)
}

chrono('GOGOGO') 
```

Enter fullscreen mode Exit fullscreen mode

*计时器*参考了*计时器*的内部范围。即使在*计时器*明显不再需要*计时器*的 1 秒钟后，该示波器仍然保持活动。因为那个 scope 还活着， *timer* 可以每秒打印一次‘GOGOGO’。

```
function myModule(){
  const name = 'Damien'
  const age = 25

  function sayMyName(){
    console.log(name)
  }

  function sayMyAge(){
    console.log(age)
  }

  return {
    sayMyAge,
    sayMyName
  }
}

const boom = myModule()

boom.sayMyAge()
boom.sayMyName() 
```

Enter fullscreen mode Exit fullscreen mode

模块模式！ *sayMyAge* 和 *sayMyName* 都是在它们的词法范围之外执行的。但是因为两者都引用了 *myModule* 内部作用域，所以该作用域保持活动状态，以便它们使用 name 和 age 变量。

闭包 ftw！