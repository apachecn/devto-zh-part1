# sweetpack:帮助消除受网络包和 babelðÿ˜‹困扰的时间

> 原文：<https://dev.to/abouthiroppy/sweetpack-helps-eliminate-time-to-suffer-from-webpack-andbabel-3m7>

repo:https://github . com/abouthorippy/sweetpack

```
$ npm i sweetpack --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

## 什么是 sweetpack？

sweetpack 帮助您构建 webpack 和 babel 的环境。sweetpack 隐藏了 webpack 和 babel 的所有通用设置，您没有花时间来编写配置。

## 目标

webpack 可以做任何事情，所以我认为它很难抽象。因此，我们的目标是首先帮助您构建一个简单的应用程序。
同样，目标是网络。

## sweet pack 能做什么？

sweetpack 支持以下功能。

*   使用 react by 选项(默认值:false)
*   通过选项使用流(默认值:false)
*   通过选项使用 css 模块(默认值:false)
*   通过选项使用 postcss-loader(默认值:false)
*   使用 html 的模板引擎(通过 html-webpack-plugin)
*   可以装载。包封/包围（动词 envelop 的简写）
*   webpack-dev-server 在开发时被默认激活并启用 HMR
*   在开发时使用 react-hot-loader@next(默认值:false)
*   生产时将哈希附加到文件名
*   生产时按扩展名分割文件(默认值:false)
*   生产时执行代码优化

## 。sweetpack . yml

sweetpach 提供配置文件。
默认设置如下。

```
entry: src/index.js
output: dist
js:
  flow: false
  react: false
css:
  modules: false
  postcss: false
html:
  filename: null
  template: null
dev:
  env: null
  port: 8080
  analyzer: false
  dashboard: true
prod:
  env: null
  url: false
  extract: false 
```

Enter fullscreen mode Exit fullscreen mode

如果找不到配置文件，将反映上述默认设置。
这个文件可以用`init`生成。

## 包含插件

使用过的插件列表这里是[这里是](https://github.com/abouthiroppy/sweetpack#activated-plugins)。

## 举例

最小配置的一个例子。

*package . JSON*T2】

```
{  "scripts":  {  "start":  "sweetpack watch",  "build":  "cross-env NODE_ENV=production sweetpack build"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

*。sweetpack . yml*

```
entry: ./lib/index.js
output: dist/bundle.js 
```

Enter fullscreen mode Exit fullscreen mode

*目录结构*

```
â”œâ”€â”€ dist
â”œâ”€â”€ lib
â”‚   â””â”€â”€ index.js
â””â”€â”€ package.json
2 directory, 2 files 
```

Enter fullscreen mode Exit fullscreen mode

如果运行`npm start`，可以在开发过程中使用 webpack-dev-server 和 webpack-dashboard 进行开发。

而你运行`npm run build`，`dist/`就被创建了。

```
dist
â”œâ”€â”€ bundle.2ea3ca43d449dd5fdc16.js
â””â”€â”€ index.html
0 directories, 2 files 
```

Enter fullscreen mode Exit fullscreen mode

html 和 js 被自动创建，散列被添加到文件名中。(也进行了优化)

当然，包括 css-loader 和 file-loader，所以你可以使用 css，png，gif 等。

**构建开发环境和生产 environment 非常容易"**

打开所有设置的示例

[https://github . com/abouthiropy/sweetpack/tree/master/samples/all](https://github.com/abouthiroppy/sweetpack/tree/master/samples/all)

## 反馈非常非常欢迎

如果你有兴趣，请使用 sweetpackðÿ™

[https://github.com/abouthiroppy/sweetpack](https://github.com/abouthiroppy/sweetpack)

希望你喜欢它！！
干杯。