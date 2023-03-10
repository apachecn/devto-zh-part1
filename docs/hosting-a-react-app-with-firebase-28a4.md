# 使用 firebase 托管 react 应用程序

> 原文：<https://dev.to/aurelkurtula/hosting-a-react-app-with-firebase-28a4>

今天，我们将把之前创建的 web 应用程序[部署到 firebase。](https://github.com/aurelkurtula/todo-app-with-react-and-firebase)

这可以使用终端来完成。你需要做的第一件事是在你的机器上安装`firebase-tools`。

```
npm install -g firebase-tools 
```

Enter fullscreen mode Exit fullscreen mode

然后登录到您的 firebase 帐户。

```
firebase login 
```

Enter fullscreen mode Exit fullscreen mode

你将被引导到默认的网络浏览器，在那里你将被提示登录你的 firebase/google 账户。

以上做一遍。

## 部署您的项目。

在终端中，您需要指向您的项目。如果你想继续，你可以在这里克隆项目，它是[教程系列](https://dev.to/aurelkurtula/creating-an-app-with-react-and-firebase---part-one-814)的一个结果。

接下来，您需要为部署设置项目。因此，运行下面的代码。

```
firebase init 
```

Enter fullscreen mode Exit fullscreen mode

这会问你一系列问题。

第一个问题是您的项目需要利用哪些特性。您可以选择多个。对于我们的情况，我们可以只选择**托管**。我们也在利用数据库，但没有利用规则，所以只需进入**托管**，点击`space`和`enter`

下一个问题是:

您想将什么用作您的公共目录？(公开)

如果您点击`enter`，将部署的默认文件夹将是`public`。但是我们知道 react 的部署文件夹叫做`build`，所以输入它，然后继续。

*配置为单页 app(将所有网址改写为/index.html)？(是/否)*

react 应用程序是一个单页应用程序，所以输入`y`然后输入`enter`

接下来，如果它询问是否应该覆盖任何内容，请选择 no。

此时，您需要构建自己的 react 应用程序(`yarn build`或`npm run build`)。

最后要部署，运行:

```
firebase deploy 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已。部署完成后，返回托管 URL

## 安全问题无法解决

如果您正在跟进我上面提到的项目，我们将通过硬编码配置规则来连接到 firebase 项目。它们位于`./src/fire.js`

```
const config = {
      apiKey: "***********",
    authDomain: "***********",
    databaseURL: "***********",
    projectId: "***********",
    storageBucket: "***********",
    messagingSenderId: "***********"
}; 
```

Enter fullscreen mode Exit fullscreen mode

部署应用程序时，检查代码的任何人都可以轻松访问上述信息。

我认为使用[环境配置](https://firebase.google.com/docs/functions/config-env)可以很容易地解决这个问题。基本上，我们能够创建只有我们自己才能访问的私有变量。

在终端中，我们将添加这个:

```
firebase functions:config:set todoservice.apikey="MaDeItUppBlaBla" todoservice.databaseurl= ... 
```

Enter fullscreen mode Exit fullscreen mode

然后，**我想**我们可以把`./src/fire.js`修改成这个

```
import functions from 'firebase-functions';
import admin from 'firebase-admin';
admin.initializeApp(functions.config().firebase);
const config = {
      apiKey: functions.config().todoservice.apikey,
    authDomain: functions.config().todoservice.authdomain,
    databaseURL: functions.config().todoservice.databaseurl,
    projectId: functions.config().todoservice.projectid,
    storageBucket: functions.config().todoservice.storagebucket,
    messagingSenderId: functions.config().todoservice.messagingsenderid,
}; 
```

Enter fullscreen mode Exit fullscreen mode

我花了很多时间尝试不同版本的`firebase-functions`和`firebase-admin`包，但显然这些只能从服务器端代码访问！

如果有人知道如何保护前端 JavaScript (react)应用程序中的配置数据。请让我知道，因为，就目前情况而言，我看不出在 firebase 中托管这种应用程序的意义(尽管根据我的研究，我猜测同构应用程序可能会解决这个问题)。还是我遗漏了什么？