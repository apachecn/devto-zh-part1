# 在 TypeScript 中编写 Node.js 模块

> 原文：<https://dev.to/dkundel/writing-a-nodejs-module-in-typescript>

关于 [Node.js](https://nodejs.org/en/) 最好的事情之一就是它庞大的模块生态系统。有了像 [webpack](https://webpack.github.io/) 这样的捆绑器，我们甚至可以在 Node.js 之外的浏览器中利用它们。让我们看看如何用 JavaScript 开发人员和 TypeScript 开发人员都可以使用的 [TypeScript](http://www.typescriptlang.org/) 构建一个模块。

在我们开始之前，请确保您已经安装了 Node.js 理想情况下，您应该拥有 6.11 或更高版本。此外，确保您已经安装了 [npm](https://www.npmjs.com/) 或类似的软件包管理器。

让我们构建一个模块，该模块公开一个函数，该函数过滤掉一个字符串中的所有表情符号，并返回表情符号短码的列表。因为谁不爱表情符号呢？

[![decorative gif of different emojis](img/b353eba3d26af4a9fcff53ae3ade7b6e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RUkE1SWt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2017/06/BYDvhRSNstCFMpuIrrcHM4PDwL3e_e_T5PaSf7uQ5FXQ7zMvPaLpqjb1d1jqeb7V7VpC5YzWeminPCESFecPqcvg-wm8noTsZwgI9L0mHnC0Syh7DYCFfVCfVETGZW-yurG6vhDY.png)

## 安装依赖项

首先为您的模块创建一个新目录，并通过在命令行中运行
来初始化`package.json`

```
mkdir emoji-search
cd emoji-search
npm init -y 
```

Enter fullscreen mode Exit fullscreen mode

产生的`package.json`看起来像这样:

```
{
  "name": "emoji-search",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们安装一些依赖项。首先通过运行:
安装 TypeScript 编译器作为`devDependency`

```
npm install typescript --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

接下来安装`emojione`模块。我们将用它将表情符号转换成它们的简码，比如ðÿ转换成`:monkey_face:`。因为我们将在 TypeScript 中使用该模块，并且该模块不直接公开类型，所以我们还需要为`emojione` :
安装类型

```
npm install emojione @types/emojione --save 
```

Enter fullscreen mode Exit fullscreen mode

安装了项目依赖项后，我们可以继续配置 TypeScript 项目了。

## 【ðÿ】配置 TypeScript 项目

首先创建一个`tsconfig.json`文件，我们将用它来定义我们的 TypeScript 编译器选项。您可以手动创建这个文件，并将下面几行放入其中:

```
{  "compilerOptions":  {  "target":  "es5",  "module":  "commonjs",  "declaration":  true,  "outDir":  "./dist",  "strict":  true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

或者，您可以通过运行:
自动生成包含所有可用选项的`tsconfig.json`文件

```
./node_modules/.bin/tsc --init 
```

Enter fullscreen mode Exit fullscreen mode

如果你决定采用这种方法，确保根据上面的 JSON 调整`declaration`和`outDir`选项。

将`declaration`属性设置为`true`确保编译器除了将类型脚本文件编译为 JavaScript 文件之外，还生成各自的类型脚本定义文件。`outDir`参数将输出目录定义为`dist`文件夹。

接下来修改`package.json`以拥有一个构建脚本来构建我们的代码:

```
{  "name":  "emoji-search",  "version":  "1.0.0",  "description":  "",  "main":  "index.js",  "scripts":  {  "build":  "tsc",  "test":  "echo \"Error: no test specified\" && exit 1"  },  "keywords":  [],  "author":  "",  "license":  "ISC",  "devDependencies":  {  "typescript":  "^2.3.2"  },  "dependencies":  {  "@types/emojione":  "^2.2.1",  "emojione":  "^3.0.3"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们配置 TypeScript 项目所要做的全部工作。让我们继续写一些模块代码吧！

## ðÿ'创建模块代码

创建一个`lib`文件夹，我们可以在其中放置所有的类型脚本文件，并在其中创建一个名为`index.ts`的文件。将下面的文字输入其中:

```
import { toShort } from 'emojione';
const EMOJI_SHORTCODES = /:[a-zA-Z1-9_]+:/g

export function findEmojis(str: string): string[] {
  // add runtime check for use in JavaScript
  if (typeof str !== 'string') {
    return [];
  }

  return toShort(str).match(EMOJI_SHORTCODES) || [];
} 
```

Enter fullscreen mode Exit fullscreen mode

运行
编译代码

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到一个新的`dist`目录，其中有两个文件，`index.js`和`index.d.ts`。`index.js`包含我们编译成 JavaScript 的所有逻辑，而`index.d.ts`是描述我们在 TypeScript 中使用的模块类型的文件。

祝贺您创建了第一个 TypeScript 和 Javascript 都可以访问的模块！让我们为发布准备模块。

## ðÿ"–准备出版

现在我们有了自己的模块，我们必须对`package.json`做三个简单的修改来准备发布模块。

1.  将`main`属性改为指向我们生成的 JavaScript 文件
2.  添加新的`types`参数，并将其指向生成的 TypeScript 类型文件
3.  添加一个`prepublish`脚本，以确保在我们发布项目之前代码将被编译。

```
{  "name":  "emoji-search",  "version":  "1.0.0",  "description":  "",  "main":  "dist/index.js",  "types":  "dist/index.d.ts",  "scripts":  {  "prepublish":  "npm run build",  "build":  "tsc",  "test":  "echo \"Error: no test specified\" && exit 1"  },  "keywords":  [],  "author":  "",  "license":  "ISC",  "devDependencies":  {  "typescript":  "^2.3.2"  },  "dependencies":  {  "@types/emojione":  "^2.2.1",  "emojione":  "^3.0.3"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

我们还应该确保在模块的安装中排除不必要的文件。在我们的例子中，`lib/`文件夹是不必要的，因为我们只需要在`dist/`目录中构建的文件。创建一个名为。忽略并将以下内容放入其中:

```
lib/ 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！ðÿž‰，现在你可以使用`npm publish`发布你的模块了。不幸的是，有人已经建立了一个名为`emoji-search`ðÿ˜•的模块，所以如果你想发布这个模块，只需将`package.json`中的名称改为另一个名称。

## ðÿ消耗模块

我们的模块的伟大之处在于，它现在可以无缝地用在 JavaScript 或 TypeScript 项目中。只需通过`npm`或`yarn` :
安装即可

```
npm install emoji-search --save 
```

Enter fullscreen mode Exit fullscreen mode

[![decorative gif of flying emojis](img/50a117ce9db05edc0c0e2bd051528718.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--o2NzaK8C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.twilio.com/blog/wp-content/uploads/2017/06/bzJAgsQpT5Yt33jc7geZ2lyT2J16paootSYAZcTiaHW6x2mLwaSnt1ks4cOoliUyhFY8cPIr3eIiY2LL3fEG7CE0Nryy03jGB64H-todm1cjAYhC9ULVdmnyUvmJFo0lfGhucuhV.png)

如果你想在不发布模块的情况下尝试一下，你也可以安装`demo-emoji-search`模块。这与 npm 上发布的代码相同。之后我们可以使用 JavaScript 中的模块:

```
const emojiSearch = require('demo-emoji-search');
console.log(emojiSearch.findEmojis("Hello ðŸ¼! What's up? âœŒï¸")); 
```

Enter fullscreen mode Exit fullscreen mode

或者在具有完整类型支持的 TypeScript 中:

```
import { findEmojis } from 'demo-emoji-search';
const foundEmojis: string[] = findEmojis(`Hello ðŸµ! What's up? âœŒï¸`);

console.log(foundEmojis); 
```

Enter fullscreen mode Exit fullscreen mode

## ðÿžš的结论

这显然只是一个非常简单的模块，向您展示发布一个在 Javascript 和 TypeScript 中都可用的模块是多么容易。

TypeScript 为模块的作者提供了许多其他好处，例如:

*   通过更好的自动完成获得更好的创作体验
*   类型安全以尽早捕捉错误，尤其是在边缘情况下
*   尖端和实验性特征(如装饰)的向下传输

正如您所看到的，在 TypeScript 中构建一个模块非常容易，可以为 JavaScript 和 TypeScript 开发人员提供非常棒的模块体验。如果你想有一个更全面的入门模板，包括一套最佳实践和工具，请查看 GitHub 上的 Martin Hochel 的 typescript-lib-starter。

我很想听听您使用 TypeScript 的经历，如果您有任何问题，请随时联系我们:

*   dkundel@twilio.com 的邮件:
*   ðÿ推特: [@dkundel](https://twitter.com/dkundel)
*   ðÿ–github:[dkundel](https://github.com/dkundel)

* * *

*[用 TypeScript](https://www.twilio.com/blog/2017/06/writing-a-node-module-in-typescript.html) 写一个 Node.js 模块最初发表于 2017 年 6 月 8 日 [Twilio 博客](https://www.twilio.com/blog)。*