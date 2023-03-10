# 澄清异步和承诺

> 原文：<https://dev.to/warrend/clarifying-async-and-promises-4h38>

我想清楚地呈现 async 和 promises，因为一开始这些概念对我来说很难理解。以下是我的尝试:

同步 javascript 意味着在进入下一个动作之前等待一个动作完成。例如，想象一排五张小桌子。所有的桌子上都有一枚硬币，只有第二张桌子例外，上面有一个旋转的陀螺。你的任务是走到每张桌子前，翻转硬币并旋转桌面。如果你同步做这件事，你会走到第一张桌子，抛硬币，然后移到下一张桌子。但是在第二张桌子上，你旋转桌面，但是你不能移动到下一张桌子，直到桌面停止旋转。在现实世界中，如果您必须等待某件事情完成后才能加载页面的其余部分，这可能会导致糟糕的用户体验。

异步请求解决了这个问题。不是等待每个函数完成，而是将动作传递给 JS 之外的一个线程。当请求完成时，它被添加到线程的末尾，回调函数将被触发。因此，在我们的桌子例子中，一旦你旋转桌面，你就可以移动到下一张桌子并投掷硬币。当顶部停止旋转时，桌子将被移动到队列的末尾，并在您到达桌子的末尾时准备好。

这关系到承诺。承诺本质上是一个对象，代表一个尚未完成但将在未来某个时间点完成的行动。这使得处理多个异步请求更加容易。当承诺解决后，我们可以使用。然后函数对返回的数据做一些事情。这样也更容易上链。然后一起请求。例如，如果我们向服务器请求令牌，我们希望等待令牌返回。一旦我们收到令牌，我们就解析它，最后对数据做一些事情。

另一个例子是，如果我们有一个应用程序发出三个独立的 API 请求。假设一个 API 请求是针对图像的，另一个是针对评论的，还有一个是针对用户数据的。但是我们希望在更新 UI 之前等待所有数据返回，这样我们就可以使用一个承诺来等待所有数据完成，然后显示给用户。我们可以利用。所有这些都是通过承诺来实现的。

作为下面说明承诺的最后一个例子，我们看到 A 将首先启动。然后一个获取请求被触发，我们等待来自 C 的承诺，然后等待 d。同时，B 在我们等待时被触发。一旦 C 解析，它就触发，最后 D 解析，它就触发。

```
apiCall = () => {
    console.log(‘A')
    fetch('http://someAPI/data')
        .then(res => {
            console.log(‘C’)
            return res.json()
    })
        .then(theData => console.log(‘D’, theData))
        console.log(‘B‘)
} 
```

Enter fullscreen mode Exit fullscreen mode