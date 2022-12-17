# 舒适的管座

> 原文：<https://dev.to/warrend/comfortable-tube-sockets-1fm9>

根据他们的网站，“Socket.io 实现了基于事件的实时双向通信。”从本质上来说，这意味着浏览器和服务器可以将实时信息即时发送给多个用户。在一个聊天应用程序中，当一个用户键入一条消息并按下 enter 键时，这条消息就传送到服务器，并从服务器发送给连接到套接字的所有其他用户。我喜欢把套接字看作是从浏览器到服务器的管道。你把信息发射到管子里，然后到达服务器。在服务器上，它被处理和复制，并被发送回所有其他开放的管道，由客户端处理。

除了聊天，套接字还用于各种应用，包括文档协作和多人视频游戏。在本教程中，我将回顾一个聊天应用程序的基本概念。因为 Socket.io 几乎可以与任何框架一起使用，所以我不会深入介绍任何设置细节，而是专注于主要概念。此外，我不会介绍如何设置 package.json 文件或使用 npm install 来安装依赖项。如果你需要更多关于这些主题的信息，请点击[查看 npm](https://www.npmjs.com) ，点击[查看 package.json](https://docs.npmjs.com/files/package.json) 。

首先，我们将从 server.js 开始，它位于项目的根目录中。首先添加以下依赖项，并创建一个新的 [Express](https://expressjs.com) 应用:

```
const express = require('express')
const socket = require('socket.io')
// open is for convenience
const open = require('open')
const app = express() 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要指定在哪里提供静态文件。这可能会根据您使用的框架而改变，但是在我的例子中，我从公共目录提供文件。然后我们需要设置服务器监听，在这个例子中，端口 8000，或者任何你喜欢的端口:

```
const express = require('express')
const socket = require('socket.io')
const open = require('open')
const app = express()
const port = 8000  

app.use(express.static('/public'))

const server = app.listen(port, function(err) {  
  if (err) {
    console.log(err)
  } else {
    open(`http://localhost:${port}`)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经运行了 Express 服务器，我们需要监听使用`io.on`连接到套接字的用户。一个用户连接，函数说，哦，我们有了一个新用户。

```
const express = require('express')
const socket = require('socket.io')
const open = require('open')
const app = express()
const port = 8000  

app.use(express.static('/public'))

const server = app.listen(port, function(err) {  
  if (err) {
    console.log(err)
  } else {
    open(`http://localhost:${port}`)
  }
})

const io = require('socket.io')(server); 

// Sets up the connection
io.on('connection', (socket) => {  
  console.log('a user connected');
}) 
```

Enter fullscreen mode Exit fullscreen mode

有了这个函数，我们可以将不同的侦听器放入其中，稍后我们将把它们与客户端连接起来。第一个将在用户发送新消息时监听。我们将使用`socket.on`,它可以取任何名字，但是在我们的例子中，我们将简单地称它为‘chat ’,以及一个回调函数。当我们的用户在一条新消息上按 send 时，这条消息通过管道被发送到被我们的`socket.on`函数捕获的地方。

一旦我们捕捉到来自客户端的消息，我们就使用`io.sockets.emit`。该函数获取消息，并通过管道将其发送到所有其他打开的套接字:

```
const express = require('express')
const socket = require('socket.io')
const open = require('open')
const app = express()
const port = 8000  

app.use(express.static('/public'))

const server = app.listen(port, function(err) {  
  if (err) {
    console.log(err)
  } else {
    open(`http://localhost:${port}`)
  }
})

const io = require('socket.io')(server); 

io.on('connection', (socket) => {  
  console.log('a user connected')

  socket.on('chat', (data) => {
    // Emits chat message to all open sockets
        io.sockets.emit('chat', data)
        console.log("Chat emitted from server")
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

您可以添加任意数量的侦听器，但是对于本教程，我们将保持简单，并添加一个在用户断开连接时向控制台发送消息的侦听器:

```
const express = require('express')
const socket = require('socket.io')
const open = require('open')
const app = express()
const port = 8000  

app.use(express.static('/public'))

const server = app.listen(port, function(err) {  
  if (err) {
    console.log(err)
  } else {
    open(`http://localhost:${port}`)
  }
})

const io = require('socket.io')(server); 

io.on('connection', (socket) => {  
    console.log('a user connected')

    socket.on('chat', (data) => {
        io.sockets.emit('chat', data)
        console.log("Chat emitted from server")
    })

    // Disconnects
    socket.on('disconnect', () => {
      console.log('user disconnected');
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在是时候转移到我们的客户端了，在我们的例子中，它将作为 client.js 运行在公共文件夹中。

首先，让我们使用`socket.io-client` :
连接我们的客户端和服务器

```
const io = require('socket.io-client')  
const socket = io('http://localhost:8000') 
```

Enter fullscreen mode Exit fullscreen mode

然后我们将为发送按钮点击设置一个事件监听器。我将让这段代码含糊不清，因为它取决于你如何在你自己的项目中连接它。在 vanilla JS 中，你可以简单地在按钮上设置一个事件监听器，或者你可以使用类似于 [React](https://reactjs.org/) 的东西。除了您所选择的内容，您还希望在内部包含以下代码，该代码将消息与任何其他相关信息(如用户名、日期等)一起发送到管道中。):

```
// Inside your event listener
    socket.emit('chat', {
        message: message,
        username: username
    }) 
```

Enter fullscreen mode Exit fullscreen mode

单击发送按钮后，Socket.io 会将聊天发送到服务器，服务器会捕获聊天并将其发送到每个打开的套接字。下一步是设置从服务器发回的数据的“捕捉器”。我们使用`socket.on`来做这件事，它监听任何通过所有管道发回的聊天:

```
socket.on('chat', (data) => {
    console.log("Received from server: " + data)
    // Do something with the data
}) 
```

Enter fullscreen mode Exit fullscreen mode

您可以决定如何处理收到的数据。您可能希望使用类似于`document.getElementById('messages').innerHTML = "<p>" + data.message + "</p>"`的东西来填充`div`，或者如果您使用类似于 React 的东西，您可以用新消息来更新状态。

我们几乎没有触及 Socket.io 的表面，但是理解基础知识让我了解了更多关于套接字的知识，因为这个概念一开始很难理解。