# ES6 中的生成器简介

> 原文：<https://dev.to/damcosset/introduction-to-generators-in-es6-5h1>

## 简介

有了 ES6，我们得到了一个新工具:**生成器**。在一个普通的函数中，只有一个入口点:函数本身的调用。生成器允许你暂停一个函数的执行，稍后再继续。生成器在处理迭代器时很有用，可以简化 Javascript 的异步特性。

## 语法

那么，和普通函数相比，我们如何定义生成器呢？通过在*函数*关键字:
之后使用*(星号)操作符来声明一个生成器函数

```
function* awesomeGenerator(){
  //code
} 
```

Enter fullscreen mode Exit fullscreen mode

为了暂停生成器内部的执行，我们使用语句 *yield* :

```
function* awesomeGenerator(){
  yield 'Hey friend' // We pause the execution here
  console.log('Back again') // When we resume, we are here
} 
```

Enter fullscreen mode Exit fullscreen mode

## 下一个()方法

生成器为您提供了一个 *next()* 方法，用于启动/恢复执行。这个方法返回一个有两个键的对象:

```
{
  value: [ yielded value ],
  done: [ true if we reach the end]
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们看一个非常简单的发生器:

```
function* groceriesGenerator(){
  yield 'Eggs'
  yield 'Tomatoes'
  yield 'Milk'
  return 'Paper Bag'
}

const groceries = groceriesGenerator()

console.log(groceries.next()) // [1]
console.log(groceries.next()) // [2]
console.log(groceries.next()) // [3]
console.log(groceries.next()) // [4]

// [1] { value: 'Eggs', done: false }
// [2] { value: 'Tomatoes', done: false }
// [3] { value: 'Milk', done: false }
// [4] { value: 'Paper Bag', done: true } 
```

Enter fullscreen mode Exit fullscreen mode

在我们的第一个*杂货. next()* 调用中，我们的 *groceriesGenerator* 到达我们的第一个 *yield* 语句并暂停，将值 *Eggs* 返回给调用者。我们的第二个 *next()* 在第二个 *yield* 语句处继续执行，再次暂停并返回*番茄*...

我们的最后一个 *next()* 终止发电机，返回*纸袋*并将 *done* 设置为真。

## 作为迭代器

在介绍中，我说过生成器可以帮助实现迭代器。我们来看一个例子:

```
function* iterationGenerator( arr ){
  for( let i = 0; i < arr.length; i++ ){
    yield arr[i]
  }
}

const iterator = iterationGenerator( ['John', 'Sarah', 'Joe', 'Emily'])

let current = iterator.next()

while( !current.done ){
  console.log( current.value )
  current = iterator.next()
}

// John
// Sarah
// Joe
// Emily 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，您可以看到生成器的状态是如何在跨调用中维护的。当我们通过调用 *next()* 来恢复执行时，变量和循环是相同的。

## 将值传回生成器

最后，您还可以将一个值传递回生成器。这里有一个例子:

```
function* twoWayGenerator(arr){
  for( let i = 0; i < arr.length; i++ ) {
    const symbol = yield 
    console.log(`${arr[i]}  ${symbol}`)
  }
}

const prices = twoWayGenerator([10, 23, 12])

prices.next()
prices.next('€')
prices.next('£')
prices.next('$')

// 10 €
// 23 £
// 12 $ 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们的第一个 *next()* 不会打印任何东西，因为我们在第一个 console.log 的之前暂停了执行*。该参数作为 yield 语句的返回值提供。我们将这个值存储在*符号*变量中，并在接下来使用它。*

您还可以强制生成器抛出一个错误:

```
prices.next()
prices.throw( new Error('Invalid Amount'))

//  const symbol = yield
//                   ^
//  Error: Invalid Amount 
```

Enter fullscreen mode Exit fullscreen mode

这就是关于发电机的快速和肮脏的介绍。在下一篇文章中，我们将深入探讨生成器如何帮助我们控制 Javascript 的异步流。