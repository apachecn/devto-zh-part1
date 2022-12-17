# 在电子中处理节点模块版本

> 原文：<https://dev.to/onmyway133/dealing-with-nodemoduleversion-in-electron>

原帖[https://github.com/onmyway133/blog/issues/69](https://github.com/onmyway133/blog/issues/69)

### 节点 _ 模块 _ 版本

今天，我试图安装[夏普](https://github.com/lovell/sharp)和`yarn add sharp`在我的电子应用程序中工作，但我得到了以下错误

> 未捕获的错误:使用
> NODE_MODULE_VERSION 57 针对不同的 Node.js 版本编译了模块“/Users/khoa/MyElectronApp/NODE _ modules/sharp/build/Release/sharp . NODE”
> 。这个版本的 Node.js 需要
> NODE_MODULE_VERSION 54。请尝试重新编译或重新安装
> 模块

### 原生节点模块

稍微搜索一下，这是因为`sharp`是一个[原生节点模块](https://nodejs.org/api/addons.html)，它使用了[函数库](https://github.com/jcupitt/libvips)

> Node.js 插件是用 C++编写的动态链接的共享对象，可以使用 require()函数将它们加载到 Node.js 中，就像普通的 Node.js 模块一样使用。它们主要用于提供 Node.js 中运行的 JavaScript 和 C/C++库之间的接口。

关于如何构建原生节点模块的更多信息可以在这里找到[https://blog . rising stack . com/writing-native-node-js-modules/](https://blog.risingstack.com/writing-native-node-js-modules/)

### 节点版本

我正在使用 [nvm](https://github.com/creationix/nvm) 管理节点版本，`nvm list`显示`8.4.0`为我正在使用的最新节点版本。

```
v6.10.1
v7.8.0
v7.9.0
v8.0.0
-> v8.4.0
system 
```

Enter fullscreen mode Exit fullscreen mode

在[节点上搜索发布](https://nodejs.org/en/download/releases/)，发现节点 8.4.0 的`NODE_MODULE_VERSION`为 57，所以这是 npm 用来编译`sharp`的节点版本

但是，我好像找不到`sharp`在用的`NODE_MODULE_VERSION` 54。我尝试了节点`8.0.0`，它被认为有`NODE_MODULE_VERSION` 54，但它不工作

### 电子版

由于这个帖子的时间，`electron`是在`7.9.0`版本，你可以在这里查看[https://github.com/electron/electron/blob/master/.节点版本](https://github.com/electron/electron/blob/master/.node-version)或者在 Javascript 控制台中运行`process.versions`

### 利用电子重建

所以在我用原生节点模块读完这个[之后，我安装`electron-rebuilder`重新编译`sharp`](https://electron.atom.io/docs/tutorial/using-native-node-modules/) 

```
yarn add electron-rebuild --dev
yarn add sharp
./node_modules/.bin/electron-rebuild 
```

Enter fullscreen mode Exit fullscreen mode

它现在工作ðÿž‰

### 阅读更多

*   [https://blog . rising stack . com/writing-native-node-js-modules/](https://blog.risingstack.com/writing-native-node-js-modules/)