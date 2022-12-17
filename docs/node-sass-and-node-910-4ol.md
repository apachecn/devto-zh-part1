# 节点-sass 和节点 9.1.0

> 原文：<https://dev.to/letsbsocial1/node-sass-and-node-910-4ol>

[![Awesome Sass](img/d3aae37ece9c5f7c4a099d699264b390.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--taPEnhxi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/13api2mh97ebj5xh384j.png)

所以前几天我发现我可以在 **React** 应用中使用`Scss`和`CSS Modules`，以及如何配置自己的`webpack-dev.config.js`和`webpack-prod.config.js`。它与 **CRA** (创建-反应-应用)的配置非常相似，但是在`CSS`的基础上增加了`SCSS`的配置。我已经测试过了，效果非常好。但这不是本文的主题。

这里我想谈谈 node-sass 与 **React 16.1.1** 的兼容性。当我用 **React** 和`SCSS`创建我的[投资组合网站](https://interglobalmedia.github.io/portfolio/#/)时， **React 16.0.0** 刚刚问世。这是我在那里使用的版本 **React** 。我最初使用的是`node-sass 4.5.3`，不记得有任何问题。

然后我最近把我的`Node`版本升级为**版本** `9.1.0`。我还有`node-sass 4.5.3`。当我试图用`npm run serve`(我自己的定制脚本)运行`webpack dev server`时，我在控制台中得到以下错误:

```
Module build failed: Error: Node Sass does not yet support your current environment: OS X 64-bit with Unsupported runtime (59) 
```

我以前从未遇到过这种情况，不知道这意味着什么。我只知道`node-sass`在控制台中抛出一个错误，我的应用程序无法呈现到页面上。在我通过谷歌搜索了一点修复方法后，我在 [node-sass 问题](https://github.com/sass/node-sass/issues/2142)中发现了以下线索:

```
Module build failed: Error: Node Sass does not yet support your current environment: OS X 64-bit with Unsupported runtime (59) #2142 
```

不同的人提出了许多不同的建议/成功的解决方案，但没有一个对我有用。除了底部的那个。该方法不起作用，但更改为建议的 node-sass 版本可以:

```
marcvangend commented 4 days ago
Downgrading Node may not be necessary. I had the same error (albeit on Linux: "Node Sass does not yet support your current environment: Linux 64-bit with Unsupported runtime (59)") running node-sass 4.5.3, installed as dependency of gulp-sass. A regular npm update will only check the top-level packages, but after running npm --depth 9999 update, node-sass was updated to 4.7.2 and the problem was solved. 
```

我所做的就是卸载`node-sass 4.5.3`，然后安装`node-sass 4.7.2`:

```
npm uninstall node-sass

npm i node-sass@4.7.2 
```

就这样了！因此，如果你得到这个错误是因为最初你可能一直在使用早期版本的`Node/NPM (< 9.1.0)`，其中你没有**与`node-sass`的兼容性问题**，而在升级到*(在我的例子中，是 9.1.0)版本的`Node/NPM`时，你突然有了这个问题，只要确保**将** `node-sass`升级到`version 4.7.2`。*

 *快乐的斗嘴！

[模块构建失败:错误:节点 Sass 尚不支持您当前的环境:OS X 64 位，运行时不受支持(59) #2142](https://github.com/sass/node-sass/issues/2142)

[CSS 模块&创建 React 应用中的 Sass](https://medium.com/@kswanie21/css-modules-sass-in-create-react-app-37c3152de9)*