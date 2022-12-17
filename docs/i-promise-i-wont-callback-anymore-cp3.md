# 我保证我不会再打电话了

> 原文：<https://dev.to/damcosset/i-promise-i-wont-callback-anymore-cp3>

## 简介

处理 Javascript 的异步本质是非常具有挑战性和令人沮丧的。很长一段时间以来，回调一直是默认的做事方式。ES6 给了我们一个承诺回调的替代方案。从版本 4 开始，Node.js 中就有了 Promises。

## 那是什么？

承诺是一种抽象，它允许函数返回一个名为 *promise* 的对象。承诺是异步操作的最终结果。当异步操作没有完成时，我们说承诺是**待定**。当操作成功完成时,**兑现了承诺。一个承诺在操作失败时被**拒绝**。**

## 许下诺言

在 ES6 中，可以用 *Promise* 构造函数创建一个承诺。它采用一个带有两个参数的函数，通常称为*解决*和*拒绝*。 *resolve* 是我们的承诺兑现时会调用的函数，当我们的承诺被拒绝时会调用 *reject* 。

让我们从一个返回承诺的函数开始。这个承诺总会兑现的。

```
const myPromise = () => {
  return new Promise( ( resolve, reject ) => {
  console.log('I promise!')
  resolve()
})
}

myPromise()
.then(() => {
  console.log('I made it!')
})

// I promise!
// I made it! 
```

Enter fullscreen mode Exit fullscreen mode

我的承诺返回一个承诺。当我们调用我们的函数时，承诺是*待定*，它既没有实现也没有被拒绝。我们打印出*我保证！*我们调用*解析*函数。 *then()* 方法负责处理已履行的承诺。 *resolve()* 调用触发 *then()* 方法，我们打印出*我成功了！*

现在让我们看看一个被拒绝的承诺:

```
 const rejectedPromise = () => {
  return new Promise( ( resolve, reject ) => {
    console.log('I promise!')
    reject('You lied to me!!')
  })
}

rejectedPromise()
.then(() => {
  console.log('I made it!')
})
.catch(err => {
  console.log('How dare you?')
  console.log(err)
})

// I promise!
// How dare you?
// You lied to me!! 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们的承诺调用了 *reject* 函数，意思是我们的承诺被拒绝。这触发了*捕捉*方法。用错误消息调用*拒绝*是一个很好的做法。注意，在这种情况下， *then()* 方法是**而不是**被调用。

## 我保证，我保证，我保证，我保证，我保证...

承诺的神奇之处在于它能够将承诺束缚住。如果我们以之前的例子为例，增加一个额外的*，那么()* :

```
rejectedPromise()
.then(() => {
  console.log('I made it!')
})
.catch(err => {
  console.log('How dare you?')
  console.log(err)
})
.then(() => {
  console.log('I forgive you no matter what.')
})

//I promise!
//How dare you?
//You lied to me!!
//I forgive you no matter what. 
```

Enter fullscreen mode Exit fullscreen mode

这个 last *then()* 会一直运行。如果我们的承诺实现了，第一个 then 将被执行，捕捉将被跳过，最后我们的最后一个 then 将被运行。

让我们创建三个承诺，并将它们链接起来:

```
 const promiseToLove = iAmMature => {
  return new Promise( ( resolve, reject ) => {
    if( iAmMature ){
      resolve('I love you so much!')
    } else {
      reject("It's not you, it's me...")
    }
  })
}

const promiseToProtect = iAmNice => {
  return new Promise( ( resolve, reject ) => {
    if( iAmNice ){
      resolve('I promise I will protect you!')
    } else {
      reject('What? Get lost!')
    }
  })
}

const promiseToBeHereOnTime = hairLooksGood => {
  return new Promise( ( resolve, reject ) => {
    if( hairLooksGood ){
      resolve('I promise I will be there!')
    } else {
      reject('How about tomorrow?')
    }
  })
}

//First promise
promiseToLove(true)
.then(statement => {
  console.log(statement)
})
.catch(statement => {
  console.log(statement)
})
//returns another promise
.then(() => promiseToProtect(true))
//handles our second promise
.then(statement => {
  console.log(statement)
})
.catch(statement => {
  console.log(statement)
})
// returns annother promise
.then(() => promiseToBeHereOnTime(true))
// handles our third promise
.then(statement => {
  console.log(statement)
})
.catch(statement => {
  console.log(statement)
})
// this always runs
.then(() => {
  console.log('And they lived happily ever after!!!')
})

// I love you so much!
// I promise I will protect you!
// I promise I will be there!
// And they lived happily ever after!!! 
```

Enter fullscreen mode Exit fullscreen mode

我们的三个函数接受一个参数(布尔值)。如果参数设置为 true，则承诺将被履行，否则，将被拒绝。一旦一个承诺被达成，我们就返回另一个并处理那个...

您能看到在处理 Javascript 的异步特性时，优雅的承诺会带来多少好处吗？不需要嵌套无限量的回调。很干净，很漂亮。我将让您想象一下，如果我们在这里使用回调而不是承诺，代码会是什么样子。

只是为了好玩，让我们把一切都设为假，因为有些人不能信守承诺...

```
//First promise
promiseToLove(false)
.then(statement => {
  console.log(statement)
})
.catch(statement => {
  console.log(statement)
})
//returns another promise
.then(() => promiseToProtect(false))
//handles our second promise
.then(statement => {
  console.log(statement)
})
.catch(statement => {
  console.log(statement)
})
// returns annother promise
.then(() => promiseToBeHereOnTime(false))
// handles our third promise
.then(statement => {
  console.log(statement)
})
.catch(statement => {
  console.log(statement)
})
// this always runs
.then(() => {
  console.log('Why are you like this?')
})

// It's not you, it's me...
// What? Get lost!
// How about tomorrow?
// Why are you like this? 
```

Enter fullscreen mode Exit fullscreen mode

## 现实生活中的承诺

在 Node.js 中，并不是所有的函数都支持现成的承诺。要解决这个问题，可以使用 util 模块中的 *promisify* 方法。它接受一个函数，并将其转换为一个返回承诺的函数。

### 克隆文件

要克隆一个文件，我们将读取其内容，然后将其写入一个新文件。回调风格，你会有这样的东西:

```
const fs = require('fs')

fs.readFile('myFile.js', 'utf-8', (err, data) => {
  fs.writeFile('clone.js', data, err => {
    if(err){
      throw err
    } else {
      console.log('All done')
    }
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们已经可以看到远处回调地狱的大门了。让我们承诺这件事。我甚至会先写一个文件，然后读它，再写一个新的，然后读我们的新克隆。是的，我知道，我疯了...

```
const fs = require('fs')

// Get the promisify method from the util module
const { promisify } = require('util')

// Promisify our readFile and writeFile function
const readFile = promisify(fs.readFile)
const writeFile = promisify(fs.writeFile)

writeFile('original.txt', 'Promise me you will clone me!')
.then(() => readFile('original.txt', 'utf-8'))
.then(content => writeFile('clone.txt', content))
.then(() => readFile('clone.txt', 'utf-8'))
.then(cloneContent => console.log(cloneContent))
.catch(err => console.log('Error occured:', err))

// Promise me you will clone me! 
```

Enter fullscreen mode Exit fullscreen mode

是啊，真性感。你为什么还要用回调来写作呢？我们的 writeFile 和 readFile 要么在调用它们的 *resolve()* 时返回文件的内容，要么在调用它们的 *reject()* 时返回错误消息。在我们的例子中，我只写了一个 *catch()* 。但是如果前面的任何承诺被拒绝，这个 *catch()* 将被调用:

```
writeFile('original.txt', 'Promise me you will clone me!')
.then(() => readFile('404NOTFOUND.txt', 'utf-8')) // <= Error here
.then(content => writeFile('clone.txt', content))
.then(() => readFile('clone.txt', 'utf-8'))
.then(cloneContent => console.log(cloneContent))
.catch(err => console.log('Error occured:', err)) // <= Trigger this

//Error occured: { Error: ENOENT: no such file or directory, open //'404NOTFOUND.txt'
//  errno: -2,
//  code: 'ENOENT',
//  syscall: 'open',
//  path: '404NOTFOUND.txt' } 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这应该足够让你开始自己的承诺了。保持理智，让你的代码更干净，使用承诺而不是回调:)