# 用 Webpack 创建微型引导构建

> 原文：<https://dev.to/kayis/creating-tiny-bootstrap-builds-with-webpack>

*TL；DR 这里是一个[库](https://github.com/kay-is/webpack-bootstrap-example)和一个工作实例。:)*

几乎每个 Web 开发人员都读过 Twitter 的 CSS 框架 [Bootstrap](http://getbootstrap.com/) 。它很容易主题化，并且在整个行业中很有名，所以如果你对 CSS 没有强烈的看法，这可能是一个很好的开始。

## 为什么？

问题是，它太大了。从内容交付网络(CDN)交付的普通引导构建大约有 120KB 大。通常客户端已经对此进行了缓存，因为许多页面使用相同的 CDN 链接，但有时您不想或可以依赖外部资源。

*越小越好*，因为*最快的位是不发送的位*而*省一便士就是赚一美元！*

这里的想法是，定制一个 Bootstrap，但不是使用 [Web 接口](http://getbootstrap.com/customize/)，而是使用 Bootstrap 和 Webpack 的较少源代码。

## 如何？

首先你通过`npm`获得引导。

接下来，您需要一些加载器和一个 Webpack 插件。`style-loader`、`css-loader`、`sass-loader`和`extract-text-plugin`。

`webpack.config.js`中的`less`文件的规则如下:

```
 {
    test: /\.less$/,
    use: ExtractTextPlugin.extract({
      fallbackLoader: 'style-loader',
      loader: [
        {loader: 'css-loader', options: {minimize: true}},
        'less-loader',
      ],
    })
  }
  ...
  plugins: [new ExtractTextPlugin('style.css')] 
```

Enter fullscreen mode Exit fullscreen mode

这将告诉 Webpack 如何加载 Less 文件，将它们编译成 CSS，然后将这个 CSS 从 JS 包中提取到一个 CSS 文件中。

此外，由于 Webpack 现在理解得更少，您可以直接使用一个更少的文件作为条目。

```
entry: {style: './src/index.less'} 
```

Enter fullscreen mode Exit fullscreen mode

引导源代码由几个较少的文件组成，所有的*由`bootstrap.less`导入*，这通常作为编译整个东西的入口点。

但是我们不想要整个东西，因为它太大了！

所以我们所做的就是将`bootstrap.less`文件的内容复制到我们自己的 less 文件中，然后注释掉我们不需要的内容。

举个例子:

```
@import "node_modules/bootstrap/less/variables.less";
@import "node_modules/bootstrap/less/mixins.less";
@import "node_modules/bootstrap/less/normalize.less";
@import "node_modules/bootstrap/less/scaffolding.less";
@import "node_modules/bootstrap/less/type.less";
@import "node_modules/bootstrap/less/grid.less";
@import "node_modules/bootstrap/less/forms.less";
@import "node_modules/bootstrap/less/buttons.less"; 
```

Enter fullscreen mode Exit fullscreen mode

如果你不使用图形图标、表格、打印视图、代码或组件，你可以**把这个包压缩到 33KB** (pre gzip)。也许你只需要基本的 CSS，并想做自己的组件，或者你想做一个简单的公式，没有太多额外的东西。

您还可以使用一个 [post-css](https://github.com/postcss/postcss-loader) 加载器来添加供应商前缀。