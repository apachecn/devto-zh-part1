# Laravel 5.5 中的 React 入门

> 原文：<https://dev.to/nathanheffley/getting-started-with-react-in-laravel-55-363>

*原贴于[nathanheffley.com](https://www.nathanheffley.com/blog/getting-started-with-react-in-laravel-5-5)T3】*

默认情况下，Laravel 的最新版本附带了 JavaScript 框架的脚手架 [Vue.js](https://vuejs.org/) 。这对快速入门很有帮助...如果你打算用它。

前端 JavaScript 的另一个流行选择是脸书的 [React](https://reactjs.org/) 。以前在 Laravel 中，如果你想在你的项目中使用 React，你必须手动删除 Vue 并添加 React。虽然这并不十分困难，但确实需要引入包，将其添加到 app.js 或 bootstrap.js 文件中，修复 webpack 文件，等等。

但是现在 Laravel 5.5 已经发布，有一个新的 artisan 命令可以为我们处理所有这些。

```
$ php artisan preset react 
```

Enter fullscreen mode Exit fullscreen mode

这个简短的命令用 React 替换了所有的 Vue 搭建。如果您想继续学习本课程的其余部分，请打开您的终端，转到您放置 Laravel 项目的任何地方。

```
$ laravel new laravel-react

$ cd laravel-react

$ php artisan preset react

$ npm install 
```

Enter fullscreen mode Exit fullscreen mode

如果你想看看已经完成的项目，我把它放在 GitHub 上。

现在您已经有了一个带有 React 脚手架的新的 Laravel 安装，您已经准备好了！在像这样的另一个项目中使用 React 可能会有点混乱，所以我们将构建一个简单的计数器组件来开始。

当您第一次运行 preset 命令时，您会得到一个默认的 React 组件，位于/resources/assets/js/components/example . js。将它重命名为 Counter.js，去掉所有的示例代码，我们只剩下这个简单得多的组件。

```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

export default class Counter extends Component {
    render() {
        return (
            <div className="container">
                <p>Counter Component</p>
            </div>
        );
    }
}

if (document.getElementById('counter')) {
    ReactDOM.render(<counter></counter>, document.getElementById('counter'));
} 
```

Enter fullscreen mode Exit fullscreen mode

我们所做的只是重命名组件并去掉多余的代码。因为我们重命名了组件文件，所以我们也必须进入我们的`/resources/assets/js/app.js`文件并在那里更新名称，所以现在我们不需要示例组件，而是需要计数器组件。

```
require('./components/Counter'); 
```

Enter fullscreen mode Exit fullscreen mode

最后，为了让组件显示在屏幕上，以便我们知道一切都按预期工作，我们可以编辑 welcome.blade.php 视图来显示我们的组件。只需删除默认的`<body>`并用这段 HTML 替换它。

```
<body>
    <div class="container">
        <div id="counter"></div>
    </div>
    <script src="/js/app.js"></script>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

因为我们在组件中使用了`ReactDOM.render(..., document.getElementById('counter'));`,它会找到那个`<div id="counter"></div>`,并把它变成我们新的计数器组件。该脚本只是我们的主 app.js 脚本，当您运行构建脚本时，所有内容都会被编译到该脚本中。说到这里，请确保运行它，因为我们已经创建了我们的组件。

```
$ npm run dev 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您在浏览器中访问您的项目，您应该会在页面上看到一个非常令人兴奋的“计数器组件”段落。

别担心，会好起来的。如果你想赶上 GitHub 项目，你可以跳到[安装并运行 Dev](https://github.com/NathanHeffley/Laravel-React/commit/f8533747811232e36466f2fa9340b442b7ecd8e4) 提交。

现在我们的组件已经显示在页面上了，这意味着我们可以开始添加一些定制功能了。既然我们说过这将是一个计数器，那就让我们履行这个承诺吧。

我们要做的第一件事是设置计数器的状态，这是当前计数将被存储的地方。回到 Counter.js 文件，将这个构造函数添加到组件声明中。

```
constructor() {
    super();
    this.state = {
        count: 0
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经声明了状态，我们也可以开始在页面上显示它。改变渲染函数，输出我们激动人心的新计数状态，而不是那个无聊的老段落。

```
render() {
    return (
        <div classname="container">
            <p>{ this.state.count }</p>
        </div>
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

再次运行您的构建脚本来编译所有这些内容，并重新加载您的页面来查看当前计数输出到屏幕上！如果你落后了或者只是想看看代码，我们刚刚完成了[设置状态](https://github.com/NathanHeffley/Laravel-React/commit/6496fb2ee5f904488ac25074f2925af2d8ceb380)提交。
尽管如此，它仍然不太像一个计数器，因为我们实际上不能与它交互来改变状态。为了使它尽可能通用，这样它就可以计算你想要的任何类型的东西，我们将简单地添加两个按钮:一个增加计数，一个减少计数。把这些放在我们输出计数的地方。

```
<button onclick="{this.increment}">+</button>
<button onclick="{this.decrement}">-</button> 
```

Enter fullscreen mode Exit fullscreen mode

这些按钮会工作得很好，但是现在我们没有增量或减量功能，所以让我们接下来添加它们。将这两个函数放在构造函数和呈现函数之间。

```
increment() {
    let count = this.state.count;
    count++;
    this.setState({count: count});
}

decrement() {
    let count = this.state.count;
    count--;
    this.setState({count: count});
} 
```

Enter fullscreen mode Exit fullscreen mode

它们非常简单。运行递增功能将使计数状态增加 1，运行递减功能将使计数状态减少 1。因为我们在组件的函数内部使用了`this`，所以不要忘记将`this`绑定到函数。要了解更多关于为什么你需要这样做和其他这样做的方法，看看这些[在 React](https://www.sitepoint.com/bind-javascripts-this-keyword-react/) 中绑定 JavaScript 的 this 关键字的 6 种方法。
就我个人而言，我将函数绑定在组件构造函数的末尾，就像这样。

```
constructor() {
    [...]

    this.increment = this.increment.bind(this);
    this.decrement = this.decrement.bind(this);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以再次成功地编译您的代码，并在 Laravel 中拥有一个全功能的 React 组件！如果您在浏览器中查看该站点，您应该会看到默认计数 0 和两个小按钮。单击其中任何一个都会改变计数并更新显示的值，正如您所期望的那样。

你有它！现在，您可以在所有的 Laravel 项目中快速轻松地开始使用 React(如果您愿意),而不必在配置文件中摸索。

你可以在 GitHub 上查看这个例子的完整源代码。