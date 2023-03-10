# 带 async/await 的异步代码

> 原文：<https://dev.to/damcosset/asynchronous-code-with-asyncawait-7cd>

## 简介

我写了关于 ES6 中引入的[承诺](https://dev.to/damcosset/i-promise-i-wont-callback-anymore-cp3)和[发电机](https://dev.to/damcosset/introduction-to-generators-in-es6-5h1)。另一种让异步代码看起来同步的方法几乎在 ES6 中实现了，但并不完全是这样:**异步/等待**。这种功能是建立在承诺之上的。让我们来看看吧。

## 语法

语法如下:你必须声明一个函数为`async` :

```
const asyncFunction = async () => {
  // This is a good start
}

// or

const asyncFunction = async function(){
  // Old school function keyword? I like it!
} 
```

然后，在这个`async`函数中，您可以使用`await`关键字来告诉函数它应该等待什么:

```
const asyncFunction = async () => {
  const step1 = await fetchingData() // Wait for this

  const step2 = await savingData() // Then wait for that

  // Do something else
} 
```

## 你仍然可以信守诺言

我提到过**异步/等待**是建立在承诺之上的。一个`async`函数返回一个承诺。这意味着你可以调用*。然后()*和*。catch()* 对他们:

```
const fs = require('fs')

// promisify is a neat tool in the util module that transforms a callback function into a promise one
const { promisify } = require('util')
const writeFile = promisify(fs.writeFile)
const readFile = promisify(fs.readFile)

const writeAndRead = async () => {
  await writeFile('./test.txt', 'Hello World')
  const content = await readFile('./test.txt', 'utf-8')

  return content
}

writeAndRead()
  .then(content => console.log(content)) // Hello World 
```

好吧，这是怎么回事？

*   我们创建一个名为*write 和 Read* 的`async`函数。
*   该函数有两个`await`关键字:首先，我们等待函数写入文件 *test.txt*
*   其次，我们等待函数读取我们刚刚写入的 *test.txt* 文件。
*   我们将它存储在一个变量中并返回它
*   因为*异步*函数返回承诺，所以我可以使用*。然后()*调用*write read()*函数后。

很可爱吧？我们甚至不再需要指定 resolve()和 reject()方法。这就引出了下一点。

## 你们对我都是同样的错误< 3

让我们假设一个场景，其中您的代码中有基于承诺的逻辑和非基于承诺的逻辑(同步和异步)。您可能会这样处理错误:

```
const someComplicatedOperation = () => {
  try {
    // Blablabla do something
    db.getUsers()     //promise
    .then( users => {
      const data = JSON.parse( users )    // ===> What if this fail bro?
      console.log(data)
    })
    .catch( err => {
      console.log('You broke your promise!!!')
    })
  }
  catch( err ){
    console.log('I caught a error. But I only catch synchronous stuff here :(')
  }
} 
```

没错。try/catch 不会捕获 JSON.parse 错误，因为它发生在一个承诺中。一个被拒绝的承诺触发了*。抓()*的方法，但**没有**的其他抓法。这很烦人，我们必须复制代码来捕捉错误。好了，那个时候已经结束了*异步/等待*！

```
const allErrorsAreDeclaredEqualInTheEyesOfAsyncAwait = async () => {
  try {
    const users = await db.getUsers
    const data = JSON.parse( users )
    console.log(data)
  }
  catch( err ){
    console.log('All errors are welcomed here! From promises or not, this catch is your catch.')
  }
} 
```

干净，简洁干净，同时简洁。优秀的老 try/catch 可以处理我们抛出的所有错误。

## 你能把它们错误堆多高？

作为开发人员，如果有一样东西是我们喜欢的，那就是错误堆栈中的无限多的函数。这可能没什么大不了的，但更像是在使用 async/await 时应该知道的一件好事。来看看:

```
const stackingAllTheWayToTheSky = () => {
  return usefulPromise()
    .then(() => usefulPromise())
    .then(() => usefulPromise())
    .then(() => usefulPromise())
    .then(() => usefulPromise())
    .then(() => usefulPromise())
    .then(() => {
      throw new Error('I can see my house from here!!')
    })
}

stackingAllTheWayToTheSky()
  .then(() => {
    console.log("You won't reach me.")
  })
  .catch(err => {
    console.log(err) // FEEL THE PAIN!
  })

  //Error: I can see my house from here!!
  //  at stackingAllTheWayToTheSky.then.then.then.then.then.then (index.js:50:11) 
```

现在使用异步/等待:

```
const debuggingMadeFun = async () => {
  await usefulPromise()
  await usefulPromise()
  await usefulPromise()
  await usefulPromise()
  await usefulPromise()
  throw new Error('I will not stack.')
}

debuggingMadeFun()
  .then(() => {
    console.log('Not here')
  })
  .catch(err => {
    console.log(err)
  })
  //Error: I will not stack
  // at debuggingMadeFun (index.js:47:9) 
```

这不是更清晰易读吗？

## 中间值

您可能编写了一些代码，在这些代码中，您执行了一个操作，然后用它来执行第二个操作。最后，第三个也是最后一个操作需要这两个值。所以，你可以这样写:

```
const withPromises = () => {
  return firstPromise()
    .then( firstValue => {
      return secondPromise( firstValue )
    })
    .then( secondValue => {
      return thirdPromise( firstValue, secondValue )
    })
}
// Or using Promise.all. It's a bit ugly, but the job is done

const withPromiseAll = () => {
  return firstPromise() 
    .then(firstValue => {
      return Promise.all([ firstValue, secondPromise(firstValue) ])
    })
    .then(([firstValue, secondValue]) => {
      return thirdPromise(firstValue, secondValue)
    })
} 
```

让我们来看看使用异步/等待有多好:

```
const withAsyncAwait = async () => {
  const firstValue = await firstPromise()
  const secondValue = await secondPromise()
  return thirdPromise( firstValue, secondValue )
} 
```

还需要我多说吗？

## 结论

嗯，async/await 是用 Javascript 编写异步代码的一种非常酷的方式。您可以在 Node.js 中试用它，因为从 7.6 版本开始它就受到本机支持。玩得开心！！