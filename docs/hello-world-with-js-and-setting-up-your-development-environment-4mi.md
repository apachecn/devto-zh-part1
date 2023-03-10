# 用 JS 创建 Hello World 并设置您的开发环境

> 原文：<https://dev.to/matt24ray/hello-world-with-js-and-setting-up-your-development-environment-4mi>

这篇文章最初发表在[DailyDrip.com](https://www.dailydrip.com/blog/hello-world-with-js-and-setting-up-your-development-environment)

今天，我们将设置我们的开发环境，并启动和运行一个基本的“Hello World”应用程序。要设置我们的开发环境，我们需要做的第一件事是选择一个文本编辑器。有很多不同的文本编辑器，比如 [Sublime](https://www.sublimetext.com/) 、 [Visual Studio Code](https://code.visualstudio.com/) 、 [VIM](http://www.vim.org/) 和[括号](http://brackets.io/)。我的偏好是 [Atom](https://atom.io/) 。

让我们下载 Atom，并制作一个简单的“Hello World”应用程序。这是为从未使用过 JavaScript 的人准备的。

我选了一个文本编辑器。现在该安装了。进入 [Atom 主页](https://atom.io/)点击下载。在 Mac 上看起来是这样的:

[![image alt text](img/54eae19988ec51ec8e996fa9cec38ae2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5GOt1pp9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dailydrip-assets.s3.amazonaws.com/DailyDrip/blog_posts/hello-world-with-js/image_0.png)

下载完 Atom 后，打开它。然后，单击“Atom”下的“安装 Shell 命令”。这将允许您从终端使用 Atom 打开文件。

[![image alt text](img/c1f3d2b9973af7bb9b4aa4575134108a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qKH_oacW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dailydrip-assets.s3.amazonaws.com/DailyDrip/blog_posts/hello-world-with-js/image_1.png)

您的文本编辑器已经可以使用了。按“CMD + Space”，键入终端，然后按回车键。

[![image alt text](img/c86d25649ee48a340f5092cf364f5a0e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--KUk6XcZD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dailydrip-assets.s3.amazonaws.com/DailyDrip/blog_posts/hello-world-with-js/image_2.png)

现在，在您的终端中创建一个文件夹。

```
 mkdir practice 
```

Enter fullscreen mode Exit fullscreen mode

如果您键入“ls ”,您将在当前目录中看到该文件夹。现在您想在该文件夹中工作。为此，您需要更改目录。幸运的是，有一个代表“更改目录”的命令“cd”。让我们改变目录。

```
 cd practice 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们在这个文件夹中创建一个 html 文件，使用' touch '命令，后跟我们要创建的文件名，文件名以'结尾。“html”。我们需要的另一个文件是 JavaScript 文件。它将以同样的方式创建，但以'结尾。js '。然后，我们可以在终端中用 atom 打开目录，因为我们已经启用了 shell 命令，并且可以看到我们的文件。

```
 touch index.html

touch main.js

atom . 
```

Enter fullscreen mode Exit fullscreen mode

[![image alt text](img/991f088ad52ac40d7a154aa587d3528a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--H4b8RiSn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dailydrip-assets.s3.amazonaws.com/DailyDrip/blog_posts/hello-world-with-js/image_3.png)

让我们将基本的 HTML 结构添加到我们的“index.html”中。

```
 <!DOCTYPE html>

<html>

<body>

</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

我们需要连接我们的。js '文件到我们的'。“html”页面。这可以通过在我们的“body”标签上添加一行代码来轻松实现。

```
 <!DOCTYPE html>

<html>

<body>

<script src="main.js"></script>

</body>

</html> 
```

Enter fullscreen mode Exit fullscreen mode

现在在 body 标签中添加一个按钮。

```
 <button>Click Me!</button> 
```

Enter fullscreen mode Exit fullscreen mode

然后，将[‘onclick’](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XUL/Attribute/onclick)添加到值为‘hello world()’的按钮中。

```
 <button onclick="helloWorld()">Click Me!</button> 
```

Enter fullscreen mode Exit fullscreen mode

此时，您可以右键单击`index.html`文件并单击“在 finder 中显示”,然后再次右键单击该文件并单击“打开方式”并选择 Google Chrome。

[![image alt text](img/8a7d6a7dee93abdb5b7b165fbe8856e4.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--AuGfLF95--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dailydrip-assets.s3.amazonaws.com/DailyDrip/blog_posts/hello-world-with-js/image_4.png)

用你的浏览器打开文件，你会看到一个按钮。

[![image alt text](img/7807f96ff8cd48b58ba06a0e5e3fb717.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5LgsCb6g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dailydrip-assets.s3.amazonaws.com/DailyDrip/blog_posts/hello-world-with-js/image_5.png)

现在，让我们让这个按钮做点什么。打开 main.js 文件并输入这个函数。

```
 function helloWorld() {

  alert( 'Hello World' );

} 
```

Enter fullscreen mode Exit fullscreen mode

那么，我们刚才做了什么？在 JavaScript 中，“function”声明您将拥有一个执行某项操作的命令。`helloWorld`只是函数的名字，因为我们的函数没有参数，所以我们只添加`()`来完成对函数的描述..然后，函数要做什么的逻辑放在花括号“{}”中。在这种情况下是“alert(‘Hello World’)；”当这个函数被调用时，将弹出一个窗口，显示“Hello World”。我们已经在 HTML 文件中为一个按钮分配了这个函数，所以让我们看看当我们单击这个按钮时会发生什么。请确保在尝试单击该按钮之前保存了这两个文件。当您单击该按钮时，您应该会收到类似于以下内容的警告。

[![image alt text](img/3fc4c03c4afd1bf9372169eddfbfb26e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8KbgRSgY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dailydrip-assets.s3.amazonaws.com/DailyDrip/blog_posts/hello-world-with-js/image_6.png)

万岁。您已经成功执行了您的第一个 JavaScript 程序。

## 一目了然

今天，我们设置了开发环境，并用 JavaScript 编写了一个简单的“hello world”程序。

## 资源

- [这篇博文的代码](https://codepen.io/matt24ray/pen/eGeKjO)

- [Atom](https://atom.io/)

- [onClick](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/XUL/Attribute/onclick)

这是一篇 JavaScript 零帖子。JavaScript Zero 是一个从零开始的 JavaScript 旅程，面向任何对学习 JavaScript 感兴趣的人，尤其是初学者。你可以在 [JavaScriptZero 标签](https://www.dailydrip.com/blog?tag=JavaScriptZero)下看到 DailyDrip.com 上所有的 JavaScript Zero 帖子。