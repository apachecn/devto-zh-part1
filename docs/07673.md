# JavaScript 中的消息传递模式

> 原文：<https://dev.to/krissiegel/messaging-patterns-in-javascript>

JavaScript 中可以应用大量的软件开发模式。例如，当一个已知的对象或元素发出您所关心的事件时，事件提供了一个很好的机制。但是如果你想在对物体或元素一无所知的情况下行动呢？这就是信息传递的闪光点。

## 到底什么是消息传递？

有很多方法来定义消息传递，尤其是在讨论计算机科学时，但是让我们直接处理代码，并通过比较事件来定义它。

事件就像单向消息。您知道一个对象可以发出一个特定的事件，所以任何代码都可以监听那个事件，但只能在那个特定的对象上。需要知道对象的存在。

```
const MyButton = document.getElementById("MyButton");
MyButton.addEventListener("click", (e) => {
    console.log("Clicked event!");
}); 
```

Enter fullscreen mode Exit fullscreen mode

消息消除了需要知道对象可以发出什么的要求。这允许一个抽象层，它不需要知道消息可能来自哪里，但仍然处理它。

```
msngr("click").on((p) => {
    console.log("Clicked message!");
}); 
```

Enter fullscreen mode Exit fullscreen mode

在第一段代码中，我们使用了一个直接绑定到 HTML `<button></button>`元素的事件监听器，但是在第二段代码中，我们只监听一个点击消息。两者都可以接收和处理相同的数据。

这并不意味着消息传递是可以接收所有 DOM 事件的灵丹妙药；相反，这表明对特定消息的处理，无论它来自 DOM 事件还是其他什么，都可以完全独立于最初发出消息的对象或元素。

## 那有点做作；举个实际的例子怎么样？

让我们看看完全使用消息传递来创建一个抽象是什么样子的。在这个例子中，我们来处理一个典型 web 应用程序的用户配置文件的更新。

```
// ProfileController.js
const nameInput = document.getElementById("name");
nameInput.addEventListener("change", (e) => {
    msngr("Profile", "Change").emit({ name: nameInput.value });
});

// Services.js
const wSocket = new WebSocket("ws://www.example.com/socketserver");
wSocket.onopen = (e) => {
    msngr("Profile", "Change").emit(JSON.parse(e.data));
};

// HeaderController.js
msngr("Profile", "Change").on((profile) => {
    console.log("Update profile name in header...");
    // Header update code here
}); 
```

Enter fullscreen mode Exit fullscreen mode

好的，我们在这里进行了很多工作，但这是一个难点:当文本框中的文本改变*或*时，当 web socket 向我们发送一个改变时，我们立即通过`Profile`主题和`Change`类别发出改变(这里的消息由主题、类别和子类别组成，但稍后会有更多内容)。这允许任何侦听的对象获取并处理消息，在我们的示例中，该消息恰好是 web 应用程序中的一个标题，它希望在用户的档案名称更新时更新它。

最好的一点是，如果所有 DOM +服务器事件都通过消息处理，那么除了直接的 UI +服务器交互之外，您可以对所有组件进行无浏览器+无服务器的单元测试，从而快速验证业务和核心应用程序逻辑。当你想重用核心逻辑，但有不同的表现形式时，比如说，一个 React 本地移动应用程序，一个电子桌面应用程序和一个传统的 web 应用程序，这就很方便了。

## 好吧那么这个 msngr.js 是什么东西？

Msngr 是一个非常小的 JavaScript 库，可以在 node.js 和 web 浏览器中工作，允许异步发送和处理消息。消息由必需的`topic`和可选的`category`和`subcategory`组成，允许从一般到非常特殊的消息处理。它对多种消息传递用例有相当多的支持，包括持久化一个有效负载，这个有效负载可以在以后注册处理程序时使用。

Msngr.js 是我在示例中使用的，因为它是一个可靠的库，而且我在编写它时对它有所偏爱。但是也有其他的图书馆可以做类似的事情，比如 [Postal.js](https://github.com/postaljs/postal.js/) 。

用 JavaScript 编写一个非常简单的消息传递/发布机制也很容易！

## 消息所有的事情

消息传递并不是一个银弹，但是，正如你可以明显看出的，我是它的超级粉丝。下次你在设计项目的时候试一试，看看它是否符合你的需要，ðÿ'

*本文最初发表于[KrisSiegel.com](https://www.krissiegel.com/publications/messaging-patterns-in-javascript)，并由原作者在此交叉发布。*