# 开玩笑，反应和嘲笑

> 原文：<https://dev.to/letsbsocial1/jest-react-and-mocks-e3n>

[![React and Jest](img/93e87bde9ca1f45569338b8b0fad8e0e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ndzNPf2z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7woc8f5i9tqbb7qxhmd3.png)

我用脸书的`Jest`来测试我的 **React** 应用程序。昨天，我运行了一个测试，以确保我对我的`Work`组件所做的更改在我的 **Portfolio React** 应用程序中通过。它没有！但是正如 iTerm2 控制台中指出的，这不是因为`Work`组件中的任何错误。这是因为我在我的`About`组件中使用了一个`.pdf`文件，而`Jest`并不喜欢它。

```
npm run test

âœ¹ âœ­

> react-universal-blog-app@1.0.0 test /Users/mariacam/Development/portfolio-react
> jest

PASS  src/sum.test.js
FAIL  src/App.test.js
â— Test suite failed to run

/Users/mariacam/Development/portfolio-reaimg/resume/mdcResume8217.pdf:1
({"Object.<anonymous>":function(module,exports,require,__dirname,__filename,global,jest){%PDF-1.3
                                                                                             ^

    SyntaxError: Unexpected token %

      at ScriptTransformer._transformAndBuildScript (node_modules/jest-runtime/build/script_transformer.js:305:1) 
```

Enter fullscreen mode Exit fullscreen mode

我将它包含在我的 webpack 配置中，以便`webpack`知道如何加载它:

```
// in webpack-dev.config.js

{
    test: /\.(pdf|jpg|png|gif|svg|ico)$/,
    use: [
        {
            loader: 'url-loader',
            options: {
                limit: 100000
            }
        },
    ]
},

// in webpack-prod.config.js

{
    test: /\.(jpg|png|gif|svg|pdf|ico)$/,
    use: [
        {
            loader: 'file-loader',
            options: {
                name: '[path][name]-[hash:8].[ext]'
            },
        },
    ]
}, 
```

Enter fullscreen mode Exit fullscreen mode

那为什么会这样？我在我的 package.json 中模拟出了要被`Jest`忽略的文件，所以我去看了看我的 ***有什么类型的文件*** 包含在那里:

```
"jest": {
  "setupFiles": [
    "raf/polyfill"
  ],
  "moduleNameMapper": {
    "\\.(|jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$": "<rootDir>/__mocks__/fileMock.js",
    "\\.(css|less)$": "identity-obj-proxy"
  }
}, 
```

Enter fullscreen mode Exit fullscreen mode

显然我没有包括`.pdf`！这是因为我在很久以后才添加了那个资产，并且没有想到把它添加到我在`package.json`的`jest`配置中。这导致了我的测试失败。我添加了`pdf`到组合中:

```
"jest": {
  "setupFiles": [
    "raf/polyfill"
  ],
  "moduleNameMapper": {
    "\\.(pdf|jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$": "<rootDir>/__mocks__/fileMock.js",
    "\\.(css|less)$": "identity-obj-proxy"
  }
}, 
```

Enter fullscreen mode Exit fullscreen mode

然后我又在`iTerm2`跑了`npm run test`。你猜怎么着？我的测试通过了！

因此，如果您使用 **Jest** 在您的 **React** 应用程序中运行测试，请确保您模拟出了某些类型的资产，否则会导致您的测试失败！我在文章的最后提供了与这个主题相关的资源链接。

开心**反应**测试用**开玩笑**！

**相关资源:**

[React 工作流，不创建 React 应用](https://interglobalmedia.github.io/react-workflow-presentation/)

[是和网络包](https://facebook.github.io/jest/docs/en/webpack.html)

[用 Jest 处理静态资产](https://facebook.github.io/jest/docs/en/webpack.html#content)

[用 Jest 测试 React 应用](https://facebook.github.io/jest/docs/en/tutorial-react.html#content)