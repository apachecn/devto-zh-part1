# 帮我完成建造一个土著的最后一步。在 Windows 10 上使用 node-pre-gyp / pkg 的节点的. NET 绑定模块。

> 原文：<https://dev.to/jochemstoel/help-me-with-this-final-step-of-building-a-native-net-bindings-module-for-node-using-node-pre-gyp--pkg-on-windows-10-6k2>

https://www.npmjs.com/package/clr 绑定 NPM[上的节点包 *clr* 。NET framework 并允许在运行时加载 DLL 文件。要 NPM 安装 *clr* 你需要一些额外的构建工具。Visual Studio 是有用的，但不是真正必要的，因为还有一个名为 *windows-build-tools* 的包。](https://www.npmjs.com/package/clr)

CLR 使用各种版本的 Node 都可以很好地构建，但是当使用更新的 Node 8.x 时，它现在需要您运行 Visual Studio 2017 来构建。

### 来自 readme.md

> 该库是在以下环境中构建和测试的:
> 
> *   Node.js v6.3.1
> *   。NET 框架 4.5
> *   Visual Studio 2015
> *   Node.js 本机模块构建环境

我们可以使用*pkg*([https://www.npmjs.com/package/pkg](https://www.npmjs.com/package/pkg))将我们的节点项目打包成一个单独的可执行文件。然而，当我将模块 *clr* 添加到它的依赖项时，可执行文件的创建是成功的，但是当使用 require()调用 *clr* 时，我收到一个错误消息，表明该模块没有自注册。

> AtsushiSuzuki 月 13 日评论
> 如果需要分发预编译模块，可以尝试 node-pre-gyp。

*node-pre-gyp* 位于 *npm* 和 *node-gyp* 之间，提供跨平台的二进制部署方法。
T8】https://www.npmjs.com/package/node-pre-gyp

我使用下面的 package.json，然后运行 *node-pre-gyp build* 。构建成功，并创建了一个 clr.node 文件。

```
{  "name":  "clr",  "version":  "0.0.18",  "engines":  {  "node":  ">=0.12 <8"  },  "description":  "Node.js binding for .NET Framework API",  "repository":  {  "type":  "git",  "url":  "https://github.com/AtsushiSuzuki/node-clr"  },  "main":  "lib/clr.js",  "scripts":  {  "test":  "mocha"  },  "dependencies":  {  "bindings":  "^1.2.1",  "nan":  "^2.4.0",  "node-pre-gyp":  "^0.6.38",  "underscore":  "^1.8.3"  },  "devDependencies":  {  "mocha":  "*",  "aws-sdk":  "2.x"  },  "bundledDependencies":["node-pre-gyp"],  "scripts":  {  "install":  "node-pre-gyp install --fallback-to-build"  },  "binary":  {  "module_name":  "clr",  "module_path":  "./lib/",  "host":  "https://clr.s3-us-west-1.amazonaws.com"  },  "keywords":  [  ".NET",  "CLR",  "Common Language Runtime",  "API Bridge"  ],  "author":  {  "name":  "Atsushi Suzuki",  "email":  "possibly.maybe.probably@gmail.com"  },  "license":  "(ISC OR WTFPL)",  "gypfile":  true  } 
```

Enter fullscreen mode Exit fullscreen mode

我快到了。我可以用 *node-pre-gyp* 构建 *clr* ，它会创建一个 *clr.node* 文件。然而，当我需要它时，它没有应该有的 init()方法。相反，它看起来像这样:

```
{ 
    import: [Function],
    getAssemblies: [Function],
    getTypes: [Function],
    createConstructor: [Function],
    getMembers: [Function],
    invokeMethod: [Function],
    getField: [Function],
    setField: [Function],
    isCLRObject: [Function],
    getType: [Function],
    isCLRConstructor: [Function],
    typeOf: [Function],
    path: 'C:\\Users\\Administrator\\Desktop\\build\\Release\\clr.node' 
} 
```

Enter fullscreen mode Exit fullscreen mode

不管我是直接要求 clr 还是使用*绑定*方法。

```
var clr = require('clr') // or require('bindings')('clr.node'), no difference 
```

Enter fullscreen mode Exit fullscreen mode

> AtsushiSuzuki 于 10 月 1 日在 GitHub
> 上评论道:太棒了！
> 
> node-clr 将 lib/clr.js 作为主脚本。lib/clr.js 利用 clr.node.
> 如果你确实需要(".")或 require("lib/clr.js ")，它会有文档化的 API。

* * *

这就是我目前被困的地方。寿司的回答没有帮助。我觉得没搞清楚这一点很愚蠢。