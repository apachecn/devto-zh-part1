# 使用 react 和 firebase 创建应用程序-第二部分

> 原文：<https://dev.to/aurelkurtula/creating-an-app-with-react-and-firebase---part-two-5hmc>

在第二部分中，我们将继续开发 todo 应用程序，但是将它链接到一个 firebase 项目。

我将假设你已经阅读了[之前的教程](https://dev.to/aurelkurtula/creating-an-app-with-react-and-firebase---part-one-814)，并且你已经从 github 中克隆了项目[，并准备好跟随。](https://github.com/aurelkurtula/todo-app-with-react-and-firebase)

我们目前拥有的代码是一个功能应用程序。你可以运行`yarn start`(或者在此之前的`yarn install`，如果你还没有安装软件包的话)并看到我们有一个 todo 应用程序，你可以在其中添加项目，将它们标记为完成，在两条路线之间导航并删除完成的项目。

今天，我们将把我们的应用程序与 firebase 集成起来。我们将最终使用 firebase 中的数据库来管理应用程序状态。

然后，在最后一部分，我们将处理认证。

## 连接并使用数据库

在我们开始编码之前，我们需要前往 firebase，创建一个项目，现在，将`read`和`write`的数据库权限规则都改为`true`。

我已经写了一篇关于 firebase 实时数据库的[介绍，指导你如何设置所有这些。我写这个系列教程是因为我相信你了解 firebase 数据库和认证服务的基础知识，如果你不了解，我建议你先阅读我的相应教程。](https://dev.to/aurelkurtula/introduction-to-firebases-real-time-database-89l)

### 配置数据库进入 react

首先，我们需要安装 firebase:

```
yarn add firebase 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要初始化 firebase，就像我们在数据库介绍教程中所做的那样。

为了保持项目的整洁，让我们创建一个`./src/fire.js`文件并添加以下内容

```
import firebase from 'firebase';
const config = {
  apiKey: "**********",
    authDomain: "**********",
    databaseURL: "**********",
    projectId: "**********",
    storageBucket: "**********",
    messagingSenderId: "**********"
};
const fire = firebase.initializeApp(config)
export { fire } 
```

Enter fullscreen mode Exit fullscreen mode

这里没有什么新东西，我们导入 firebase，复制并粘贴 firebase 给我们的配置代码——当我们在[firebase 实时数据库介绍](https://dev.to/aurelkurtula/introduction-to-firebases-real-time-database-89l)创建项目时。然后我们出口它。我们不使用`export default`的原因是因为我们稍后将从这里导出不同的认证方法。

### 数据库积垢

前往`./src/App.js`，让我们开始与数据库交互。我们将修改现有的代码，这样我们将在数据库中执行这些操作，而不是在本地状态下创建、读取、更新和删除项目。

快速回顾一下，目前我们有一个几乎没有硬编码项目的状态。我们真正想要的是从数据库中填充状态。让我们开始吧。

```
// no constructor 
state = {
  items: {}
}
itemsRef = fire.database().ref('items') 
```

Enter fullscreen mode Exit fullscreen mode

我们从状态中删除了硬编码的数据，然后实例化数据库引用的一个实例(我们基本上告诉 firebase 我们需要一个`items`对象用于这个项目)。

最后，使用名为`componentWillMount`的`App`组件(由`React.Component`继承)的“生命周期方法”之一，我们可以用数据库数据填充状态。

```
componentWillMount(){
  this.itemsRef.on('value', data=> {
    this.setState({
      items: data.val()
    })
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

从文件中我们了解到

> componentWillMount()在挂载发生之前立即被调用。它在 render()之前被调用，因此在此方法中同步调用 setState()不会触发额外的呈现。

因此，这听起来是实现这一目的的正确的“生命周期方法”。

[这篇文章](https://daveceddia.com/where-fetch-data-componentwillmount-vs-componentdidmount/)指出

> 在 ES6 风格的类组件中，构造函数扮演着与 componentWillMount 相同的角色，所以如果你已经有了一个构造函数，你可以把代码放在那里。

尽管我们没有构造函数，但知道我们可以这样做还是很好的:

```
 constructor(props) {
    super(props);
    this.state = {
      items: {}
    }
    this.itemsRef = fire.database().ref('items')
    this.itemsRef.on('value', data=> {
      this.setState({
        items: data.val()
      })
    })
  } 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们还应该在组件卸载时移除数据库绑定。用这种方法执行任何必要的清除，如使计时器失效，取消网络请求

```
 componentWillUnmount(){
    fire.removeBinding(this.itemsRef)
  } 
```

Enter fullscreen mode Exit fullscreen mode

#### 创建物品

在`./src/App.js`中，我们已经有了在本地状态下添加项目的功能。我们不再需要这样做，我们只是直接与数据库交互，让 firebase 的“实时”特性更新状态。

```
 addItem=(e)=> {
    e.preventDefault();
    this.itemsRef.push({
      item: this.todoItem.value, 
      completed: false     
    })
  } 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们已经讨论了这个功能。请记住，与设置本地状态时不同，firebase 会自动为我们生成唯一的标识符。所以在 firebase 上，上面的代码会生成类似这样的内容:

```
{
    "items": {
      "-L1Ds8FqPAPsFiXgUhQ2" : {
        "completed" : false,
        "item" : "Get Milk"
      }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 修改项目

接下来，我们已经有了将`completed`从`false`更改为`true`的功能。

```
 completeItem=(id)=>{  
    this.itemsRef.update({
      [id]:{
        ...this.state.items[id], 
        completed: true      
      }
    })
  } 
```

Enter fullscreen mode Exit fullscreen mode

类似于我们已经做的，但这一次，我们正在更新数据库内容。

#### 删除项目

使用与我们探索 [firebase 数据库](https://dev.to/aurelkurtula/introduction-to-firebases-real-time-database-89l)时相同的代码，让我们删除一个已完成的项目。

```
deleteItem = (id) => {

    this.itemsRef.update({

      [id]: null

    })

  } 
```

Enter fullscreen mode Exit fullscreen mode

### 
 [T3】
结论](#conclusion) 

就是这样。该应用程序还没有准备好生产，因为我们基本上允许任何人向我们的数据库添加数据。在这个小系列的最后一部分，我们将使用身份验证来限制谁可以读写数据。

到目前为止，这个项目可以在同一个 [github 库](https://github.com/aurelkurtula/todo-app-with-react-and-firebase/tree/part-two)中找到。正如你将看到的，我使用了不同的分支来对应这个系列的每一部分。敬请期待[第三部也是最后一部](https://dev.to/aurelkurtula/creating-an-app-with-react-and-firebase---part-three-2c03)