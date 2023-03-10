# 面向初学者的 ES6 第 2 部分

> 原文：<https://dev.to/srebalaji/es6-for-beginners-part-2-ko>

本文第一部分出现在[这里](https://dev.to/srebalaji/es6-for-beginners-with-example-c7)。我在那里介绍了一些有趣的特性。:)

我将在这篇文章中讨论的话题

1.  承诺
2.  异步/等待

## 承诺

承诺是 ES6 中有用的特性之一。它们用于进行异步操作，如 API 请求、文件处理、下载图像等。

那么，什么是异步呢？(如果您已经知道，请稍等)

异步操作需要一些时间来完成。

例如，假设您正在定义一个向服务器发出 API 请求的函数。该函数不会立即返回输出。从服务器获得响应只需几秒钟。

因此，如果你调用这个函数，并将其值(即输出)赋给某个变量，它将是未定义的。因为 Javascript 不知道函数在处理一些异步操作。

那我们怎么处理呢？

在此之前，让我们来谈谈历史。

在承诺之前，程序员习惯于定义回调。回调是 Javascript 中的正常函数，在异步操作完成时执行。例如，你定义了一个向服务器发出 API 请求的函数。然后你提到了一个回调函数，当我们得到服务器的响应时，这个函数就会被执行。

所以在上面的例子中，Javascript 不会停止执行，直到我们从 API 得到响应。我们已经定义了一个函数(callback ),它将在得到响应后执行。我想你明白了。

### 那么，什么是承诺呢？

承诺是帮助进行异步操作的对象。

从技术上讲，它们是代表异步操作完成的对象。(如果你不明白，和我呆一会儿。)

在解释如何定义承诺之前，我先解释一下承诺的生命周期。

我们有三种状态的承诺

1.  **Pending** :在这种状态下，promise 只是执行异步操作。例如，它向服务器发出一些 API 请求，或者从 cdn 下载一些图像。从这种状态开始，承诺可以变为“已履行”或“已拒绝”

2.  **已完成**:如果承诺已经达到这个状态，那么就意味着异步操作已经完成，我们已经有了输出。例如，我们有来自 API 的响应。

3.  **拒绝**:如果承诺已经达到这种状态，说明异步操作不成功，我们有错误导致操作失败。

行..让我们看一些代码。

```
const apiCall = new Promise(function(resolve, reject) {
 // async operation is defined here...
}); 
```

Enter fullscreen mode Exit fullscreen mode

Promise 是通过使用 new 关键字创建构造函数来定义的。然后构造函数会有一个函数(我们称之为 executor 函数。)

异步操作是在 executor 函数中定义的。

注意，executor 函数有两个参数 resolve 和 reject。第一个参数 resolve 实际上是一个函数。它在 executor 函数中被调用，表示异步操作成功，我们已经得到了输出。解决功能有助于将承诺从待定状态变为已履行状态。希望你明白了。:)

像 resolve 一样，reject 也是一个函数。它也在 executor 函数中被调用，它表示异步操作不成功，我们得到了一个错误。拒绝有助于承诺从待定状态变为拒绝状态。:)

```
const apiCall = new Promise(function(resolve, reject) {
 if ( API request to get some data ) {
  resolve("The request is successful and the response is "+ response);
 }
 else {
  reject("The request is not successful. The error is "+error);
 }
}); 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，你可以看到我们在 executor 函数中做了一些异步操作。然后，如果我们从服务器得到响应，就调用 resolve 函数。如果有一些错误，就用错误消息调用拒绝函数。

我们已经完成了对承诺的定义。让我们看看如何执行 promise 并处理输出。

```
// calling the promise.
apiCall 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。我们完了。:) :)

开玩笑的。还没完呢。

在上面的代码中，调用函数并执行承诺(即执行人函数)。然后根据输出调用解决或拒绝功能。

但是你可以看到我们没有处理从承诺返回的输出。例如，如果我们从 API 得到响应，那么我们必须处理这个响应。或者如果我们得到了错误，我们需要正确地处理它。

### 那么我们如何处理呢？

我们使用处理程序从承诺中获取输出。

处理程序只是一些在事件发生时执行的函数，比如点击按钮，移动光标等等。

因此，我们可以使用处理程序来处理何时调用 resolve 函数或 reject 函数。

简单。:)

让我们来看看一些代码

```
// calling the promise with some handlers.
apiCall.then(function(x) {console.log(x); }) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们为承诺附加了一个处理程序。然后，处理程序获得一个函数参数。那么函数参数本身有一个参数 x。

发生了什么事？

然后，当 promise 内部调用 resolve 函数时，处理程序执行其函数参数。

我试着再解释一遍。

then 处理程序查找调用 resolve 函数时的事件。因此，当 resolve 函数被调用时，then 处理程序执行它的函数参数。

```
apiCall.then(function(x) {console.log(x); })

// Output
The request is successful and the response is {name: "Jon Snow"} 
```

Enter fullscreen mode Exit fullscreen mode

同样，还有另一个处理程序捕捉。

捕捉处理器寻找拒绝功能。

调用 reject 函数时，Catch 函数执行其函数参数。

```
apiCall.then(function(x) {console.log(x); }).catch(function(x) {console.log(x); })
// Assuming the request is not successful ( reject function is called in the promise. )

Output:
The request is not successful 
```

Enter fullscreen mode Exit fullscreen mode

我想你明白了。

上面的代码可读性不太好。所以我们试着重构一下。

```
apiCall
.then(function(x) {
 console.log(x); 
})
.catch(function(x) {
 console.log(x);
}) 
```

Enter fullscreen mode Exit fullscreen mode

啊…现在可以看了。大多数程序员都是这样写的。

好的..所以我认为你已经取得了很大的进步。

让我们回顾一下。

1.  Promise 是用带有函数参数的 new 关键字定义的。那么函数本身有两个函数参数 resolve 和 reject。

2.  当操作成功时，应调用函数 resolve。

3.  当操作失败时，应该调用函数 reject。

4.  **然后**处理程序寻找解析函数。

5.  **Catch** 处理器寻找剔除功能。

6.  确保代码的可读性:)

下面是 jsfiddle 中的工作示例。如果你是新手，请练习。

[JavaScript 示例中的承诺](https://jsfiddle.net/zx1ejrv8/2/)

希望你理解这个例子。很直白。

## 异步/等待

如果你理解承诺，那么 Async/Await 是很容易的。如果你没有得到承诺，Async/Await 可以帮助你理解它。也许你也能从承诺中得到明确的解脱。:)

**异步**

Async 关键字使任何函数只返回承诺。

例如，考虑下面的代码

```
async function hello() {
 return "Hello Promise..!"
} 
```

Enter fullscreen mode Exit fullscreen mode

函数 hello 将返回一个承诺。

上面的代码等同于下面的代码。

```
function hello() {
 return new Promise(function(resolve, reject) {
 // executor function body.
 });
} 
```

Enter fullscreen mode Exit fullscreen mode

简单吧？

再比如:

```
async function hello(a, b) {
 if (a < b) {
  return "Greater";
 }
 else {
  return new Error("Not Greater");
 }
}
hello(14, 10)
.then(function(x) {
 console.log("Good..! " + x); 
})
.catch(function(x) {
 console.log("Oops..! " + x); 
})

Output:
Oops..! Not Greater. 
// if you call hello(4, 10) you get "Good..! Greater" 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们定义了一个异步函数，并返回了一些值或一些错误。

如果在 async 函数中返回某个值，就相当于调用 resolve 函数。

如果您通过使用“new”调用错误构造函数(即，返回某个错误),那么它相当于拒绝函数。

别忘了异步函数会返回一个承诺。当然，你也可以在异步函数中调用 resolve 和 reject 函数。

让我们看看它是如何工作的。

```
async function Max(a, b) {
 if (a > b) {
  return Promise.resolve("Success");
 }
 else {
  return Promise.reject("Error");
 }
}
Max(4, 10)
.then(function(x) {
 console.log("Good " + x); 
})
.catch(function(x) {
 console.log("Oops " + x); 
});

Output:
Oops Error
// If we pass Max(14, 10) then we should get "Good Success" :) 
```

Enter fullscreen mode Exit fullscreen mode

**等待**

顾名思义，它让 Javascript 一直等到操作完成。假设您正在使用 await 关键字发出一个 API 请求。它让 Javascript 一直等待，直到从端点得到响应。然后它继续执行。

好的..让我们深入一点

Await 只能在异步函数内部使用。它在异步函数之外不起作用

让我们来看一个例子

```
async function hello() {
 let response = await fetch("https://api.github.com/");
 // above line fetches the response from the given API endpoint.
 return response;
}
hello()
.then(function(x) {
 console.log(x); 
});
...
...

Output:
Response from the API. 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，您可以看到我们在从 API 获取响应时使用了 await。

获取操作可能需要几秒钟的时间来获得响应，直到执行暂停，稍后再继续。

注意，await 操作只暂停 hello 函数内部的执行。hello 函数之外的其余代码不会受到影响。在函数之外继续执行。当我们得到响应时，里面的函数参数就会被执行。

希望你明白了。

让我们看一个例子

[Javascript 示例中的 Async/Await](https://jsfiddle.net/ybaep9bt/1/)

在上面的例子中，你可以看到我们为 getResponse 函数使用了 await。

getResponse 将在 5 秒钟后返回输出或错误。因此，在此之前，执行会暂停，然后返回响应。

让我们看一些实时例子。

[异步/等待示例](https://jsfiddle.net/2rt4q7j6/2/)

在上面的例子中，你可以看到我们使用了多个等待。因此，对于每个 wait，执行都会停止，直到收到响应，然后恢复。

用一些无效的 URL 尝试同样的例子。您可以看到出现了错误。

### 错误处理

异步函数中的错误处理非常简单。如果错误是在异步函数内部引发的，或者当错误是由在异步函数内部使用 await 调用的其他函数引发的，那么将调用 reject 函数。简单。

希望你喜欢。我将在下一篇文章中讨论更多有趣的话题，如数组映射、数组过滤、reduce 等。敬请期待:)

如果你喜欢这篇文章，试着点赞并分享:)