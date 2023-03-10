# 谁赢了这场比赛？管理竞争条件

> 原文：<https://dev.to/benhayehudi/who-is-winning-the-race-managing-the-race-condition>

您已经建立了一个从 API 获取数据的漂亮的 web 应用程序。ReactJS 前端用从 API 接收的数据更新状态，并用新信息重新呈现组件。API 向您发送回一个项目数组，因此您自然有一些简单的迭代器函数来遍历数组并呈现每个项目。你的语法写得很好。您可以观察您的应用程序发出获取请求并接收数据。您会看到您的状态用新数据进行了更新。然而，无论你做什么，你的应用程序都会返回一个错误。那是什么意思？为什么会这样？

欢迎来到现代 web 开发中最常见的问题之一。它可以被称为很多东西，但它通常被称为竞争条件。Javascript 按照接收的顺序运行，不会等一个项目完成后再继续下一个项目。在我们的示例中，我们可能有以下代码:

```
fetch('/api/awesomeListOfThings/', request)
      .then(data => data.json())
      .then(data => this.setState({ myAwesomeState: data })) 
```

然后，我们想用类似这样的东西来迭代数据:

```
this.state.myAwesomeState.map(coolStuff =>
        <RenderCard coolStuff={coolStuff} key={coolStuff.id} />) 
```

这应该工作得很好，除了我们的程序在进入下一步之前不会耐心地等待获取操作完成。当获取还在进行时，我们的程序想要开始迭代 myAwesomeState。但是，myAwesomeState 中还没有任何内容！这正是竞态条件描述的问题。

我们如何解决这个问题？解决这个问题的一个简单方法是让我们的应用程序忙于其他事情，而获取请求仍然在工作。这也为我们的用户提供了一个好处，他们现在会知道他们最喜欢的应用程序不只是崩溃了，而是他们想要的信息仍然在加载。我们可以通过创建一个新的对象，在获取完成前默认为`false`状态，在获取完成后默认为`true`状态，然后使用它在我们的呈现中设置一个条件语句:

```
fetch('/api/awesomeListOfThings/', request)
      .then(data => data.json())
      .then(data => this.setState({ myAwesomeState: data, finishedLoading: true })) 
```

```
this.state.finishedLoading?
    this.state.myAwesomeState.map(coolStuff =>
        <RenderCard coolStuff={coolStuff} key={coolStuff.id} />) :
    "All your awesome information is still loading, please hang on..." 
```

首先，我们检查`finishedLoading`是否为`true`，这只有在获取请求完成时才会发生。如果是`true`，那么我们继续迭代我们现在拥有的数据。如果不是`true`，我们会向用户显示一条友好的消息，让他们在 API 请求仍在处理时不要挂断。一旦`finishedLoading`状态更新到`true`，那么我们条件的第一部分将被实现，我们将不再经历我们不能`.map()`过度定义的错误。

既然我们已经为假设的 web 应用程序解决了这个问题，我们可以深呼吸一下了...然后转向我们需要解决的其他无数问题。编码毕竟是 5%写新代码，95%解决问题调试！