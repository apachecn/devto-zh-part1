# 简单来说:后端代码、前端代码以及它们如何交互

> 原文：<https://dev.to/hugo__df/in-simple-terms-backend-code-frontend-code-and-how-they-interact>

## 看一看 web 开发不断变化的边界

这最初是作为一个答案发布在 Quora 上的:
[一个前端代码和一个后端代码是如何相互交互的？](https://www.quora.com/How-does-a-frontend-code-and-a-backend-code-interact-with-each-other/)

让我们从快速定义开始:

## 前端

浏览器可以读取、显示和/或运行的所有内容。这意味着 HTML、CSS 和 JavaScript。

HTML(超文本标记语言)告诉浏览器“什么”是内容，例如“标题”、“段落”、“列表”、“列表项”。CSS(层叠样式表)告诉浏览器如何显示元素，例如，“第一段之后有 20px 的边距”，“body”元素中的所有文本都应该是深灰色，并使用 Verdana 作为其字体”。

JavaScript 告诉浏览器如何使用轻量级编程语言对一些交互做出反应。很多网站实际上并不怎么使用 JavaScript，但是如果你点击了某个东西，而在显示新内容之前页面没有闪烁成白色，这意味着 JavaScript 在某个地方被使用了。

## 后端

所有在服务器上运行的东西。“不在浏览器中”或“在连接到网络(通常是互联网)的电脑上回复其他电脑的信息”是后端。

对于你的后端，你可以使用你的服务器上的任何工具(它只是一台被设置来回复信息的计算机)。这意味着你可以使用任何通用编程语言，比如 Ruby，PHP，Python，Java，JavaScript/Node，bash。这也意味着你可以使用许多数据库管理系统，比如 MySQL，PostgreSQL，MongoDB，Cassandra，Redis，Memcached。

# 后端-前端交互的状态

目前有两种主要的架构定义了后端和前端的交互方式。

## 服务器渲染的应用

第一种是对服务器呈现的应用程序的直接 HTTP 请求。这是一个系统，通过它浏览器发送一个 HTTP 请求，服务器回复一个 HTML 页面。在收到请求和响应之间，服务器通常会查询数据库并将其输入模板(ERB、刀锋、EJS、车把)。

一旦页面被加载到浏览器中，HTML 就定义了内容、外观以及任何特殊的交互。

## 使用 AJAX 通信

AJAX 代表异步 JavaScript 和 XML。这意味着浏览器中加载的 JavaScript 从页面内部发送一个 HTTP 请求(XHR，XML HTTP 请求),并在历史上得到一个 XML 响应。

现在回复也是 JSON 格式的。

这意味着您的服务器需要有一个端点来回复 JSON/XML 请求。REST 和 SOAP 就是这种协议的两个例子。

## 客户端(单页)应用程序

AJAX 允许您在不刷新页面的情况下加载数据。这已经被 Angular 和 Ember 等框架推到了极致。这些应用程序被捆绑，发送到浏览器，任何后续渲染都在客户端(浏览器)完成。这些前端使用 JSON/XML 响应通过 HTTP 与后端通信。

## 通用/同构应用

React 和 Ember(以及其他)库和框架允许你在服务器(后端)和客户端(前端)上渲染应用程序。当像这样设置时，应用程序使用 AJAX 和服务器渲染的 HTML 来进行前端到后端的通信。

# 超越前端和后端

## 独立前端

您将要构建的 web 应用程序对网络连接的需求越来越少。

渐进式 web 应用程序只需加载一次，就可以永远运行(ish)。您可以在浏览器中拥有一个数据库。对于某些用例，您的应用程序在首次加载时只需要一个后端，然后只需要同步/保护数据。这个持久层意味着大部分逻辑都在客户端应用程序(前端)中。

## 轻量级后端

后端变得越来越轻量级。像文档存储和图形数据库这样的技术意味着后端服务对数据的重新聚合要少得多。客户端有责任指定它需要什么数据(图形数据库)或者获取它需要的所有不同的数据片段(REST APIs)。

我的意思是，由于像 AWS Lambda 这样的无服务器架构，我们现在构建的后端服务甚至不会一直运行，只是在需要的时候才运行。

## 模糊的线条

复杂性正在跨越前端/后端界限。我们现在可以选择，根据我们正在构建的应用程序的类型，让我们的客户端拥有复杂性，或者将复杂性保留在服务器上。

每种选择都有利弊。也就是说，服务器是一个更稳定、未知更少的环境，但它总是一个网络调用。某些用户拥有最新的浏览器，可以从一个客户端应用程序中获益，该应用程序通过一个简洁的用户界面完成大部分工作，但你可能会疏远那些没有在光纤互联网连接上运行最新浏览器的用户。

归根结底，拥有所有这些选择是一件好事。只要我们专注于使用合适的工作工具来打造出色的体验和产品。希望您已经对当今的 web 开发状态有了更多的了解。如果你喜欢这篇文章，记得分享。在 twitter 或 [@hugo__df](https://twitter.com/hugo__df) 上关注我，了解更多网络开发内容。

[简单来说:后端代码、前端代码以及它们如何交互](https://hackernoon.com/in-simple-terms-backend-code-frontend-code-and-how-they-interact-2485c5a1bbd2)最初发表于 2017 年 3 月 31 日 https://medium.com/@hugo__df。