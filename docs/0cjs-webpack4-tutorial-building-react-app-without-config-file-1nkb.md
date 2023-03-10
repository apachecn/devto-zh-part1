# 0cjs！Webpack4 教程:构建不带配置文件的 React 应用程序。

> 原文：<https://dev.to/shisama/0cjs-webpack4-tutorial-building-react-app-without-config-file-1nkb>

~~1 月，webpack4 预发布为 beta 0。~~
2 月 25 日，webpack4 发布🎉
Webpack4 默认不需要配置文件！
这让我很兴奋，我试图在没有 webpack 配置的情况下构建我的 React 应用。

# 关于 webpack4

请参考下面的链接。

*   [发布 4.0.0-beta.0 webpack/webpack](https://github.com/webpack/webpack/releases/tag/v4.0.0-beta.0)
*   webpack 4 测试版—立即试用！
*   [Webpack 4 教程:所有你需要知道的，从 0 Conf 到生产模式](https://www.valentinog.com/blog/webpack-4-tutorial/)
*   [发布 4.0.0 版 webpack/webpack](https://github.com/webpack/webpack/releases/tag/v4.0.0)

# 其他工具

*   巴别预设环境
*   巴别塔-预设-反应
*   流动

# 我的网页包配置

```
const path = require('path');
const webpack = require('webpack');

module.exports = {
  entry: {
    'index': [
      path.resolve(__dirname, 'src/index.js')
    ]
  },
  output: {
    filename: '[name].js',
    path: path.resolve(__dirname, 'public'),
    publicPath: '/',
  },
  plugins: [
    new webpack.DefinePlugin({
      'process.env.NODE_ENV': JSON.stringify(process.env.NODE_ENV)
    }),
    new webpack.optimize.UglifyJsPlugin(),
  ],
  module: {
    rules: [
      {
        test: /\.(js|jsx)$/,
        use: 'babel-loader',
      },
    ]
  },
  resolve: {
    extensions: ['.js', '.jsx'],
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

# 如何使用 webpack4

## 安装

您必须安装 webpack 4 和 webpack-cli。在 webpack 4 中，webpack-cli 是在命令行上执行 webpack 所必需的。

```
npm install --save-dev webpack webpack-cli 
```

Enter fullscreen mode Exit fullscreen mode

## 构建时不配置

### 模块-绑定

首先，我在没有配置和选项的情况下执行了 webpack。
出现错误。

```
Module parse failed: Unexpected token (15:6)
You may need an appropriate loader to handle this file type.
|   render() {
|     return (
|       <div> 
```

Enter fullscreen mode Exit fullscreen mode

上述错误意味着 babel 无效，webpack 不分析 jsx 语法。使用 config，下面的代码解决了这个错误。

```
module: {
  rules: [
    {
      test: /\.(js|jsx)$/,
      use: 'babel-loader',
    },
  ]
}, 
```

Enter fullscreen mode Exit fullscreen mode

如果你想使用 babel-loader，你需要使用`--module-bind`选项。
用法:`--module-bind {extension}={loader-name}`

```
webpack --module-bind 'js=babel-loader' --module-bind 'jsx=babel-loader' 
```

Enter fullscreen mode Exit fullscreen mode

### 解析-扩展

虽然`--module-bind`选项有效，但出现了其他错误。

```
Module not found: Error: Can't resolve './Foo' in '/path/to/src' 
```

Enter fullscreen mode Exit fullscreen mode

这个错误意味着导入模块失败，
这个错误发生的原因是我忽略了如下代码的扩展。

```
import Foo from 'components/Foo' 
```

Enter fullscreen mode Exit fullscreen mode

下面的配置解决了这个错误。

```
resolve: {
  extensions: ['.js', '.jsx'],
}, 
```

Enter fullscreen mode Exit fullscreen mode

Webpack4 有`--resolve-extensions` cli 选项。
使用`--module-bind`和`--resolve-extensions`选项，
设置应该用于解析模块的扩展(例如`--resolve-extensions .js,.jsx` )

```
webpack --module-bind 'js=babel-loader' --module-bind 'jsx=babel-loader' --resolve-extensions .js,.jsx 
```

Enter fullscreen mode Exit fullscreen mode

### 模式

好的，错误已经解决😊但是显示警告。

```
WARNING in configuration
The 'mode' option has not been set. Set 'mode' option to 'development' or 'production' to enable defaults for this environment. 
```

Enter fullscreen mode Exit fullscreen mode

引用自[版本说明 4.0.0-beta.0](https://github.com/webpack/webpack/releases/tag/v4.0.0-beta.0)

*   你现在必须在两种模式之间做出选择:生产还是开发
    *   生产支持各种优化来生成优化的包
    *   开发启用注释和开发提示，并启用 eval devtool
    *   WIP:开发模式中的附加提示
    *   生产不支持观看，开发针对快速增量重建进行了优化
    *   生产还支持模块连接(范围提升)
    *   您可以使用优化中的标志对此进行详细配置。*(构建您的自定义模式)
    *   process.env.NODE_ENV 设置为生产或开发(仅在构建的代码中，不在配置中)
    *   有一个隐藏的无模式，禁用一切

必须添加选项。

```
webpack --module-bind 'js=babel-loader' --module-bind 'jsx=babel-loader' --resolve-extensions .js,.jsx --mode production 
```

Enter fullscreen mode Exit fullscreen mode

### 条目

您可以指定入口点。
`--entry src`表示切入点是`src/index.js`。
`--entry src/foo.js`表示切入点是`src/foo.js`

### 输出

此外，您可以指定输出点。
`--output public`表示输出目录为`public`。
`--output public/index.bundle.js`表示输出目录为`public`，输出文件名为`index.bundle.js`。
T5 是`--output`的简写。

### 最终

```
webpack --module-bind 'js=babel-loader' --module-bind 'jsx=babel-loader' --resolve-extensions .js,.jsx --mode production --entry ./src --output public/index.bundle.js 
```

Enter fullscreen mode Exit fullscreen mode

## 其他选项

用`--help`选项可以知道其他选项。

```
webpack --help 
```

Enter fullscreen mode Exit fullscreen mode

# 进一步阅读

*   [发布 4.0.0-beta.0 webpack/webpack](https://github.com/webpack/webpack/releases/tag/v4.0.0-beta.0)
*   webpack 4 测试版—立即试用！
*   [Webpack 4 教程:所有你需要知道的，从 0 Conf 到生产模式](https://www.valentinog.com/blog/webpack-4-tutorial/)
*   [保持 webpack 快速运行:提高构建性能的现场指南](https://slack.engineering/keep-webpack-fast-a-field-guide-for-better-build-performance-f56a5995e8f1)

谢谢你。