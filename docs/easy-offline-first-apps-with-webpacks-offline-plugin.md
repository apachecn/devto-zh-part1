# 使用 Webpack 的离线插件轻松离线第一应用程序

> 原文：<https://dev.to/kayis/easy-offline-first-apps-with-webpacks-offline-plugin>

*TL；DR 这里是一个[库](https://github.com/kay-is/webpack-offline-plugin-example)和一个工作实例。:)*

你们中的许多人可能已经注意到了所有关于离线第一网络应用的传言。网络上充斥着关于 *AppCache* 关于*服务人员*之类的文章，现在你会问自己，*我怎样才能让我的应用程序离线工作，就像酷孩子*一样。或者你简单地问，**为什么我的设备上已经有的数据不再显示给我？！**

嗯，有很多技术可以帮助你，但这似乎是一个相当快节奏的话题。所以首先要做的是。

如果你写一个单页应用程序，你的应用程序经常会发送不同类型的请求。一些得到图像，一些得到样式，一些代码和一些*实际用户数据*。

## 缓存用户数据

对于最后一点，即*用户数据*，你基本上可以将所有数据存储在客户端的某个地方。因为这主要取决于您的业务逻辑，所以建议您自己编写至少部分逻辑。

如果键值存储足够了，您可以使用 [localStorage](https://en.wikipedia.org/wiki/Web_Storage) 来实现这一点，或者如果您需要更复杂的数据存储，可以使用[索引的 DB](https://developer.mozilla.org/en-US/docs/Web/API/IndexedDB_API) 。甚至还有帮助使用这些低级 Web-API 的库。像 [PouchDB](https://pouchdb.com/) 和[local feed](https://github.com/localForage/localForage)。

## 缓存资产

但是这篇文章是关于你的资产的。你的 HTML，CSS，JS 和图片。

如果你用 Webpack 捆绑你的资产，有一个非常简单的方法:离线插件。

使用这个插件构建之后，您可以调用一个函数，使所有 Webpack 生成的资产可以离线使用。

一个基本的`webpack.config.js`可能是这样的:

```
const HtmlWebpackPlugin = require('html-webpack-plugin')
const OfflinePlugin = require('offline-plugin')

const html = new HtmlWebpackPlugin({template: './src/index.html'})
const offline = new OfflinePlugin

module.exports = {
  entry: './src/index.js',
  output: {
    filename: '[name].[hash].js',
    path: './assets/',
  },
  plugins: [html, offline],
} 
```

Enter fullscreen mode Exit fullscreen mode

## 陷阱:非 Webpack 资产

现在你可能会问，为什么基础版需要另一个插件？嗯，*离线插件*有一个陷阱。

如果没有传递任何配置对象，它将*只*缓存 Webpack 产生的资产。

因此，如果你有一个`index.html`，而你*没有用 web pack*生成，它不会被缓存，当没有连接时，你的整个应用程序不会显示。

使用 *html 插件*是解决这个问题的一种方法。然后会包含`index.html`。

另一种方法是使用`externals` :

```
const offline = new OfflinePlugin({
  externals: ['index.html'],
}) 
```

Enter fullscreen mode Exit fullscreen mode

这也用于来自另一个服务器的资产，例如，如果您需要来自内容交付网络(CDN)的 Bootstrap 或 jQuery。

一开始我觉得有点混乱，因为我把`externals`想成了 API 请求和 CDN 资产。但基本意思是:*不是由 Webpack* 构建的文件

基本配置不会给你任何警告，只有当你尝试手动配置*缓存*时，你可能会看到:

> OfflinePlugin 中的警告:在输出资产中找不到缓存资产[index.html ],如果它是外部资产，请将其放到|externals|选项中以删除此警告

## 陷阱:永远缓存

基本方法是在代码开始时要求*离线插件*运行时并安装它。这基本上意味着*启用缓存*。

```
const offlinePluginRuntime = require('offline-plugin/runtime')
offlinePluginRuntime.install() 
```

Enter fullscreen mode Exit fullscreen mode

但是您不必在应用程序启动时自动激活缓存。如果你不习惯离线模式，这可能会导致意想不到的行为。

例如，您可以上传文件的另一个版本，并给它相同的名称，这样它在客户端上就不会改变。

或者在`externals`中有一个 API 调用，现在只显示缓存的结果。

这对于调试非常有帮助。

## 结论

离线插件是一种非常简单的向用户展示已经下载的数据的方式，即使他们不在线。如今，越来越多的人通过移动设备上网，这种离线场景变得越来越不可思议。