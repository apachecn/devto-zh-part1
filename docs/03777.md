# Meteor、React 和 FlowRouter:快速设置

> 原文：<https://dev.to/damcosset/meteor-react-and-flowrouter-quick-setup-6g5>

## 简介

Meteor 已经成为我们团队的首选 Javascript 框架。我们在前端将它与 React 结合使用。我想我会写一个快速入门的教程，教你如何使用流星+反应。此外，我还将谈到路由的 FlowRouter。如果你还没有安装 Meteor，你可以按照这个[页面](https://www.meteor.com/install)的说明进行操作

## 创建应用程序

你回来了？好，我们开始吧。首先要做的是创建一个新的 Meteor 应用程序。我们使用以下命令来实现这一点:

`meteor create <nameOfYourApp>`

一旦完成，你就有了一个新的 Meteor 应用程序在“nameOfYourApp”文件夹中。

*   导入文件夹

我们将在新应用程序的顶部创建一个名为 *imports* 的文件夹。*导入*是流星里的一个特殊文件夹。只有找到一个*导入*语句来导入文件，里面的文件才会被加载。

*   client/main.js 和 client/main.html

您将删除客户端文件夹中这两个文件的所有内容。默认情况下，Meteor 使用 Blaze 模板。我们不需要它。以下文取代 main.html 的内容:

```
<head></head>
<body>
  <div id='root'></div>  
</body> 
```

Enter fullscreen mode Exit fullscreen mode

没什么特别的，只是在为 React 做准备。

*   添加 react 和 react-dom

我们需要这些:

`meteor npm install --save react react-dom`

*注意:*在一个 Meteor 应用程序中，当安装新的包时，在你的命令前面加上 *meteor* 。

*   前件

为了清楚起见，我在/imports 文件夹中添加了一个/ui 文件夹。我喜欢把客户端代码和服务器端代码分开。但是你不必这样做。在这个 */ui* 文件夹中，我创建了我的第一个组件 App.jsx:

```
import React from 'react'

const App = () => {
  return 'Home'
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

同样，没有什么疯狂的，只是返回一个简单的字符串。

*   相应地更新我们的 client/main.js

我们的 html 文件已经准备好了，我们需要编写代码来呈现我们的组件。这是基本的反应，大概是这样的:

```
 import React from 'react'
import { Meteor } from 'meteor/meteor'
import { render } from 'react-dom'

import App from '/imports/ui/App'

Meteor.startup(() => {
  render( <App />, document.getElementById('root'))
}) 
```

Enter fullscreen mode Exit fullscreen mode

当我们的应用程序启动时， *Meteor.startup()* 中的代码将会运行。

您可以使用`meteor`启动您的应用程序，进入 localhost:3000 并查看我们的组件是否正确呈现。太好了，现在你可以使用流星反应。

## 实现流量路由器

在这一部分，我们将使用 FlowRouter 包添加一个路由器。您可以通过以下方式安装它:

`meteor add kadira:flow-router`

使用 *meteor add* 安装 Meteor 环境中的包。你可以用*流星移除*来移除它们。

我们还需要 *react-mouter* 包来安装我们的路线布局:

`meteor npm install --save react-mounter`

*   配置我们的路线

我们将在/client 文件夹的 routes.jsx:
文件中定义我们的路由

```
import React from 'react'
import { FlowRouter } from 'meteor/kadira:flow-router'
import { mount } from 'react-mounter'

import App from '/imports/ui/App'
import HomePage from '/imports/ui/HomePage'
import AboutPage from '/imports/ui/AboutPage'

FlowRouter.route('/', {
  name: 'Home',
  action(){
    mount( App, {
      content: <HomePage />
    })
  }
})

FlowRouter.route('/about', {
  name: 'About',
  action(){
    mount( App, {
      content: <AboutPage />
    })
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们用 *FlowRouter.route()* 定义路由。第一个参数是路由的路径。这里我们需要“/”和“/about”。然后，我们给它一个名字，并在 action()方法中使用 react-mounter *mount* 方法。 *mount* 方法将布局作为第一个参数。在我的示例中，我们之前创建的组件应用程序将用于该目的。它将保存路由内容。然后，我们指定应该在布局中呈现哪些组件。如果路径为'/'，将呈现组件主页，在路径'/about '处将呈现 AboutPage。

我正在渲染的组件将可以在带有 this.props.content 的应用程序中访问，因为我选择了键名“content”。你可以自由选择你想要的名字。

*   改编我们的 App.jsx 组件

我们的应用程序组件现在看起来像这样:

```
import React from 'react'

const App = props => {
  return props.content
}

export default App 
```

Enter fullscreen mode Exit fullscreen mode

*   主页和关于页面组件

最后，我们需要创建关于页面和主页的两个组件:

```
 // /imports/ui/HomePage.jsx
import React from 'react'

const HomePage = () => {
  return 'This is the homepage'
}

export default HomePage

// /imports/ui/AboutPage.jsx
import React from 'react'

const AboutPage = () => {
  return 'This is the about page'
}

export default AboutPage 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。现在，您的应用程序中有两条路线。玩得开心！