# 将 Angular 应用部署到 Surge

> 原文：<https://dev.to/nioperas06/deploy-angular-apps-to-surge-c1f>

我通常使用 Heroku 来托管我的 Angular 应用程序。它运行良好，但我想尝试其他东西:一些新的，聪明的东西。我听说了两个专门面向前端开发者的部署工具:Netlify 和 Surge。在本文中，我将向您展示如何将 Angular 应用程序部署到 Surge。

## 什么叫浪涌？

Surge 是一款无需离开命令行即可发布 HTML5 应用的简单工具。浪涌是强大的。我有时花 10 或 20 分钟在 Heroku 上发布 Angular 应用程序，因为你需要创建一个 Procfile，在应用程序的依赖项上添加 express 和 angular-cli 等等。但有了 Surge，你需要构建你的应用，它就活了！

## 怎么做？

首先使用 npm 安装浪涌:

```
$ npm install -g surge 
```

Enter fullscreen mode Exit fullscreen mode

新建一个角度 app:

```
$ ng new angular-surge
$ cd angular-surge 
```

Enter fullscreen mode Exit fullscreen mode

您现在需要做的是为生产环境构建应用程序。

```
$ ng build --prod 
```

Enter fullscreen mode Exit fullscreen mode

现在 angular-cli 将构建工件放入 dist 文件夹。您需要使用 surge 部署此文件夹。让我们用命令行进入 dist 文件夹并开始部署。

```
$ cd dist
$ surge 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。帐户创建将在您第一次运行时进行。Surge 将向您展示您的应用程序在哪里运行。尽情享受吧！

使用 Surge，您可以做更多事情。自定义域，404 页面，SSL 等等。您可以从这里开始使用 Surge 的这些功能。

不要忘记，使用 Surge，您可以在一分钟或更短的时间内部署 Angular 应用程序！！！

编码快乐！
原载于[媒体](https://medium.com/@nioperas06/deploy-angular-apps-to-surge-7ee763db2235)2018 . 1 . 14。