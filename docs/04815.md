# 在 Node.js v8.5 实验模块中导入本地 JSON

> 原文：<https://dev.to/geoff/import-local-json-in-nodejs-v85-experimental-modules>

如果你错过了，Node.js v8.5.0 于 9 月 12 日发布，带来了许多新功能和修复。

在这些功能中，我非常兴奋的一项是 Node 中对 ES2015+模块的(实验性)支持。为了使用“ESModules ”,必须:

*   转换所有节点文件以使用`.mjs`扩展名
*   将所有`require()`和`module.exports`语句分别改为`import`和`export`语句
*   使用这些模块在`--experimental-modules`标志下运行任何节点文件/应用程序

Axel Rauschmayer 博士撰写的这篇文章介绍了新系统的更多细节，以及一些例子和需要注意的潜在问题。

这很好，但是如果您从一个`package.json`文件导入本地数据或信息呢？如果您使用的是`import`语句，那就不行了，因为导入并不像`require`函数那样解析文件，JSON 文件也不使用必要的`.mjs`文件扩展名。

您可以将所有 JSON 文件转移到`.mjs`文件中的 Javascript 对象语法，或者您可以使用我编写的的[片段来使用`fs`模块导入数据。在这种情况下，您不需要对已经使用了`require()`的 JSON 文件进行任何更新，因为代码片段中的`require`函数与节点函数具有相同的结果。](https://github.com/geoffdavis92/require.mjs/blob/master/snippet.js)

下面是使用标准 CommonJS 模块之前的一个示例应用程序:

```
// package.json
{
  // ...
  "version": "1.0.0",
  // ...
}

// logVersion.js
const logVersion = (version) => console.log(`Hello World v${version}`);
module.exports = { logVersion }

// index.js
const { version } = require('./package.json');
const logVersion = require('./logVersion.js');

logVersion(version); // -> Hello World v1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

…同样的应用程序使用了实验模块和我的`require()`片段:

```
// package.json
{
  // ...
  "version": "1.0.0",
  // ...
}

// logVersion.js
const logVersion = (version) => console.log(`Hello World v${version}`);
export default logVersion

// index.js
import fs from 'fs';
import logVersion from './logVersion';

const require = (filepath, encoding = "utf8") =>
    JSON.parse(fs.readFileSync(filepath, { encoding }));

const { version } = require('./package.json');

logVersion(version); // -> Hello World v1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

最后，我想创建一个模块，但是在解决模块中的路径时遇到了一些问题；如果您想帮助调试和/或开发该功能，请随时提交[拉请求](https://github.com/geoffdavis92/require.mjs/pulls)或[问题](https://github.com/geoffdavis92/require.mjs/issues/new)。

我在 Github 上的片段可以在这里找到: [geoffdavis92/require.mjs](https://github.com/geoffdavis92/require.mjs/blob/master/snippet.js) 。

尽情享受吧！