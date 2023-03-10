# ES6 和 Babel 入门

> 原文：<https://dev.to/chuksfestus/getting-started-with-es6-and-babel-2j91>

[![](img/f4bd0121153785669ca676d26e11d248.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nTv05aVW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AGMLD_bKgczsoj0tDBMAVsA.jpeg)

ES6(也称为 ES2015)是 javascript 语言的更新，包括许多新的改进。

多年来，javascript 社区一直在“入侵”语言编写库和实用程序，为语言添加有用的特性。Prototype、jQuery、angularJs、下划线、Lodash、backbone 和其他一系列工具都有助于扩展 javascript 的功能。ES6 将这些扩展中的许多作为原生特性，这意味着通过编写现代 javascript，您可以减少对额外实用程序库的依赖。

让我告诉你如何开始！

### 用 ES2015 插件安装巴别塔

我们将从安装 Babel CLI 和一个来自 NPM 的预置软件包开始，这样我们就可以使用新的语法添加:

```
npm install --save-dev babel-cli babel-preset-env 
```

Enter fullscreen mode Exit fullscreen mode

巴别塔有许多插件，用于各种语言修改(如 JSX)，但这一个插件是我们需要开始。

### 创建一个. babelrc 文件

这个小文件允许我们为巴别塔用法创建一个预置:

```
{
  "presets": ["env"]
} 
```

Enter fullscreen mode Exit fullscreen mode

至此，我们已经安装了 Babel 并设置了首选项！

### 创建您的 JavaScript 文件！

这是有趣的部分开始，将玩新的语法！下面是一个非常简单的带有箭头函数的代码片段:

```
((con) => {
  ['yes', 'no'].forEach((item) => {
    con.log(item);
  })
})(console); 
```

Enter fullscreen mode Exit fullscreen mode

很酷吧？！

好了，样本创建完毕，让我们把它转换成“传统”的 JavaScript，供尚不支持 ES2015 的浏览器使用！

### 转存 JavaScript

有了 Babel 和准备好要处理的 JavaScript 代码，我们将触发这个过程:

```
./node\_modules/.bin/babel src -d dest 
```

Enter fullscreen mode Exit fullscreen mode

该命令传输 src 目录中的每个 JavaScript 文件，并将其放在 dest 目录中。我们的样本 JavaScript 文件变成:

```
'use strict';

(function (con) {
  ['yes', 'no'].forEach(function (item) {
    con.log(item);
  });
})(console); 
```

Enter fullscreen mode Exit fullscreen mode

给你…您现在可以开始使用 ES6 了！！