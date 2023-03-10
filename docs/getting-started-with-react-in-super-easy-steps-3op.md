# 以超级简单的步骤开始使用 React。

> 原文：<https://dev.to/sarah_chima/getting-started-with-react-in-super-easy-steps-3op>

React 最近获得了麻省理工学院的重新许可。几周前发布了 React 16 及其令人敬畏的新功能。用 React 开始学习和构建很酷的项目是多么好的时机啊！但是要开始，您需要为 React 设置一个开发环境。这篇文章将向你展示两种简单的方法。首先，让我们简单了解一下 React 是什么。

React 是一个用于构建用户界面的 JavaScript 库。它是由脸书开发和维护的，它是麻省理工学院许可的，这意味着你可以在任何项目中自由使用它。不过，对于使用 React 来说，很重要的一点是，你要很好地理解 HTML 和 Javascript(ES6)。

所以让我们开始吧。使用 React 开始构建应用程序的两个超级简单的方法是:

1.  使用 Codepen。
2.  使用 Create React App 创建新的应用程序。

其他方法包括使用 CDN 并将其添加到现有项目中，您可能需要使用编译器(如 Babel)和捆绑器(如 Webpack 或 Browserify)。但是我们想要简单的方法，对吗？

**使用密码笔**

我喜欢 codepen，如果你像我一样，你可能想开始使用 Codepen。这样做很容易。以下是要遵循的步骤:

1.  创建新笔或转到现有笔。

2.  点击笔右上角的**设置**。

3.  在页面底部，有一个**快速添加**的下拉菜单，点击它并选择 React，对 ReactDOM 做同样的操作。这将把 React 和 ReactDOM 的 cdn 添加到 pen 中。

4.  最后一步是给笔添加一个 JavaScript **预处理程序**。为什么这很重要？React 使用 ES6 和 JSX，为了让浏览器理解它们，它需要一个预处理器，在这种情况下，一个编译器将它们转换成它能理解的普通 javascript。如果你不熟悉这些术语，不要担心，你很容易理解它们。对于 React 来说，巴贝尔是一个很好的选择。
    导航到 JavaScript 预处理器的下拉菜单，选择 Babel，是的，你已经创建了一个使用 React 的笔。

**使用 Create React App**

创建 React 应用程序是使用 React 开始构建的一个超级简单的方法。它设置您的开发环境，并附带预安装的软件包，使您可以立即开始构建 React 应用程序。这些软件包包括 Babel、webpack、EsLint、Autoprefixer、Jest 等。

要使用它，您需要在计算机上安装 Node 版本 6 或更高版本。因此，如果你的 Node 版本低于这个版本，你必须更新它。
按照以下步骤进行设置:

全局安装一次 Create React App，这样你就可以用它在你机器上的任何地方创建 React App。

```
npm install -g create-react-app 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成，你就可以开始创建令人敬畏的 React 应用程序了。所以下一步是实际创建一个应用程序。你可以通过跑步做到这一点

```
create-react-app name-of-your-project 
```

Enter fullscreen mode Exit fullscreen mode

这可能需要一段时间来完成，但是当它完成时，您将在当前文件夹中有一个名为`name-of-your-project`的目录。
记住将您的项目名称改为您想要的名称。

接下来要做的是将 cd 放入你的文件夹

```
cd name-of-your-project 
```

Enter fullscreen mode Exit fullscreen mode

然后

`npm start`或`yarn start`

这将在开发模式下运行应用程序。要查看这一点，请在浏览器中打开 localhost:3000，在那里您已经设置好了 react 应用程序。

如果您转到由这个过程创建的目录，您会注意到它附带了许多文件。创建应用程序所需的两个文件是位于`src`文件夹中的`index.js`和位于`public`文件夹中的`index.html`。

此外，Create React App 并没有假设一个节点后端，你可以使用任何你想要的后端技术。不错！我知道没错。

现在你有了两种简单的方法来开始使用 React。