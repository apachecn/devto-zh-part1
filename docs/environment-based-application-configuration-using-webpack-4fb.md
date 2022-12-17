# 使用 Webpack 的基于环境的应用程序配置

> 原文：<https://dev.to/kbariotis/environment-based-application-configuration-using-webpack-4fb>

我经常使用 webpack。我经常使用的一个常见模式是如何管理不同环境之间的特定于应用程序的配置。我希望能够为我的团队工作流的每个环境设置不同的 API URLs、访问代码和功能标志。

在这篇文章中，我将尝试解释为什么我们需要应用程序特定的配置，我正在使用它。然后我将向您展示两种常见的技术，以及我为什么选择第二种技术。正如右边的水平滚动条所示，它相当小。

## 举个例子

先说一个简单的 app。当然，它什么也不做。这只是一个理论上完全可行的应用:

```
import ApolloClient, { createNetworkInterface } from 'apollo-client';
import gql from 'graphql-tag';
import Raven from 'raven-js';

const client = new ApolloClient({
  networkInterface: createNetworkInterface({
    uri: 'https://graphql.example.com',
  }),
});

Raven.config('https://4a426bd18cd86e90b186dcbfa3ce1b0d@sentry.io/321321').install();

client.query({
  query: gql`
    query TodoApp {
      todos {
        id
        text
        completed
      }
    }
  `,
})
  .then(data => console.log(data))
  .catch(error => Raven.captureException(error)); 
```

Enter fullscreen mode Exit fullscreen mode

请注意这个简单代码片段中的各种常量。我们有 GraphQL 的 API Url 和 Sentry 的访问代码。我们希望为两个不同的环境构建上面的代码片段，一个是生产环境，另一个是暂存环境。但是我们希望生产环境与我们的生产 GraphQL API 对话，而暂存环境与暂存环境对话。Sentry 访问代码也是如此，每个环境都不同(Sentry 支持错误标签，这只是一个例子。)

太好了，那我们重写:

```
import ApolloClient, { createNetworkInterface } from 'apollo-client';
import gql from 'graphql-tag';

const client = new ApolloClient({
  networkInterface: createNetworkInterface({
    uri: __CONFIG__.graphql.endpoint,
  }),
});

Raven.config( __CONFIG__.sentry.code).install();

client.query({
  query: gql`
    query TodoApp {
      todos {
        id
        text
        completed
      }
    }
  `,
})
  .then(data => console.log(data))
  .catch(error => Raven.captureException(error)); 
```

Enter fullscreen mode Exit fullscreen mode

太好了！注意我输入的常数。现在我们需要根据环境适当地分配这些常量。

在此之前，让我们更进一步。我希望 Sentry 只在生产环境中启用。我需要一个拨动开关。

```
import ApolloClient, { createNetworkInterface } from 'apollo-client';
import gql from 'graphql-tag';

const client = new ApolloClient({
  networkInterface: createNetworkInterface({
    uri: __CONFIG__.graphql.endpoint,
  }),
});

if ( __CONFIG__.sentry.is_enabled) {
  import Raven from 'raven-js';
  Raven.config( __CONFIG__.sentry.code).install();
}

const logger = (e) {
  if (Raven) {
    Raven.captureException(e);
  } else {
    console.error(e);
  }
}

client.query({
  query: gql`
    query TodoApp {
      todos {
        id
        text
        completed
      }
    }
  `,
})
  .then(data => console.log(data))
  .catch(error => logger(error)); 
```

Enter fullscreen mode Exit fullscreen mode

同样，`__CONFIG__.sentry.is_enabled`只会在生产环境中成立。当它为假时，永远不会初始化它。

## 寻找解决办法

现在让我们来看看我们如何实现这一点。首先，我们需要配置管理。 [node-config](https://github.com/lorenwest/node-config) 非常适合这种情况。只需安装它，编写一些配置文件，并像这样将其加载到您的 webpack 配置中:

```
const config = require('node-config'); 
```

Enter fullscreen mode Exit fullscreen mode

`config`这里是您定义的特定于环境的配置。现在我们需要将它注入到我们的应用程序入口文件中。

一种方法是创建一个模块别名。然后你可以从你需要的每个文件中导入。虽然您可能需要稍微调整一下，以便与`node-config`一起使用，但它相当简单，并且在大多数情况下都有效。

这里需要注意的一点是，配置被导入到您的应用程序中，并且不管您是否使用它，它都只是停留在那里。没有被使用的配置值仍然存在，这甚至可能被认为是一个安全缺陷。我们不想那样。

更好的解决方案是 webpack 的[定义插件](https://webpack.js.org/plugins/define-plugin/)。DefinePlugin 将在编译时用相应的值替换您传递给它的每一个键。

所以这个:

```
if ( __CONFIG__.sentry.is_enabled) {
  import Raven from 'raven-js';
  Raven.config( __CONFIG__.sentry.code).install();
} 
```

Enter fullscreen mode Exit fullscreen mode

会变成

```
if (false) {
  import Raven from 'raven-js';
  Raven.config( __CONFIG__.sentry.code).install();
} 
```

Enter fullscreen mode Exit fullscreen mode

在登台环境中。更重要的是，webpack 将通过它(使用 UglifyJS)并完全删除它，因为它永远不会被使用。

小心点。

> 注意，因为插件直接进行文本替换，所以给它的值必须包含字符串本身的实际引号。通常，这是通过使用替代引号(如“production”)或使用 JSON . stringify(“production”)来实现的。

因此，您需要分别编写您的配置。

webpack 配置将如下所示:

```
const path = require('path');
const webpack = require('webpack');
const config = require('config');

module.exports = {
  entry: './index.js',
  plugins: [
    new webpack.DefinePlugin({
      __CONFIG__ : packinize(config)
    })
  ],
  output: {
    filename: 'app.bundle.js',
    path: "/path.join(__dirname, 'dist')/"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将`node-config`传递给`packinize`，后者将传递每个值并将其字符串化。然后我们将结果输入 DefinePlugin。

## 得出结论

我尽可能多地使用配置文件。对于根据环境、API URLs、访问代码、功能标志、拨动开关而变化的常量。能够基于这些值操纵最终代码真是太棒了！

我已经在我的 [webpack-patterns 集合](http://github.com/kbariotis/webpack-patterns)中添加了一个例子。看一看，让我知道你的想法。

当您使用 webpack 时，您是如何使用特定于应用程序的配置的？或者其他捆绑者？