# react-apollo:一种全局处理错误的方法

> 原文：<https://dev.to/andre/react-apollo-an-approach-to-handle-errors-globally-jg>

嗯，我今天有一段不寻常的旅程，我想和你分享一下。和你们大多数人一样，我也是 GraphQL 和 T2 Apollo Stack 的超级粉丝。这两种技术+ React:声明性呈现+声明性数据获取——还有什么能让开发人员更开心的吗？我想有很多事情，但无论如何。今天困扰我的一件事是全局处理错误。ðŸ˜

想象以下场景:发生了意外错误。非常糟糕的事情。UI 不能也不应该从那种状态中恢复。您希望显示一个完全不同的 UI 来通知用户这种情况。Apollo 客户端，或者确切地说是`react-apollo`绑定，在处理本地级别上发生的错误时非常好。类似于:您有一个“绑定”到 GraphQL 查询的组件，每当出现错误时，您将在*中显示与那个*组件不同的内容:

```
 import { compose } from "recompose";
import { graphql, gql } from "react-apollo";

import { ErrorHandler } from "./components";

const NewsList = compose(
  graphql(gql`
    query news {
      id
      name
    }
  `)
)(({ data }) =>
  <div>
    {data.loading ? (
      <span>Loading ...</span>
    ) : data.errors ? (
      <ErrorHandler errors={data.errors} />
    ) : (
      <ul>
        data.news.map(entry => <li key={entry.id}>{entry.name}</li>)
      </ul>
    )}
  </div> ); 
```

Enter fullscreen mode Exit fullscreen mode

这种方法没有任何问题，除了它不能满足我们渴望的场景，我们希望显示一个用户不能“逃离”的 UI。那么，我们如何实现这一目标呢？

## 后车来救！

Apollo 客户端附带了一个名为 [Afterware](http://dev.apollodata.com/core/network.html#networkInterfaceAfterware) 的机制。一个`Afterware`让你有可能直接进入 Apollo 客户机的网络层。这是一个每当来自服务器的响应被客户机处理时就执行的函数。一个例子:

```
// graphql/index.js

import ApolloClient, { createNetworkInterface } from "react-apollo";

const createClient = ({ endpointUri: uri }) => {
  const networkInterface = createNetworkInterface({ uri });

  networkInterface.useAfter([{
    applyAfterware({ response }, next) {
      // Do something with the response ...
      next();
    }
  }]);

  return new ApolloClient({ networkInterface });
};

export { createClient }; 
```

Enter fullscreen mode Exit fullscreen mode

在深入研究如何处理实际错误之前，我想通过定义如何创建实际的客户端并在您的应用程序中使用它来完成这个示例。下面是启动应用程序的入口组件:

```
// index.js
import { render } from "react-dom";
import { ApolloProvider } from "react-apollo";

import { App } from "./App";
import { createClient } from "./graphql";

const $app = document.getElementById("app");

const client = createClient({
  endpointUri: "https://api.graph.cool/..."
});

render(
  <ApolloProvider client={client}>
    <App />
  </ApolloProvider>
, $app); 
```

Enter fullscreen mode Exit fullscreen mode

原来如此。创建客户端并将其传递给`ApolloProvider`。现在怎么办？我向你保证过，我们不想显示一个不允许用户与应用程序交互的场景。经过一些修补，我得出结论，有一个简单的解决办法。这是一个愚蠢的想法:让我们给`createClient`函数传递一个额外的函数，叫做`onError`，它接受一个错误对象并在`$app` DOM 节点上执行一个全新的`render`。这将允许我们卸载损坏的 UI，并呈现一个不同的组件，用于向用户ðÿ显示相应的错误情况

首先:我们通过定义`onError`函数并将其传递给`createClient`调用:
来调整 app 的自举

```
// index.js
import { render } from "react-dom";
import { ApolloProvider } from "react-apollo";

import { App } from "./App";
import { createClient } from "./graphql";

const $app = document.getElementById("app");

const client = createClient({
  endpointUri: "https://api.graph.cool/...",
  onError: error => render(
    <MyErrorHandler error={error} />
  , $app)
});

render(
  <ApolloProvider client={client}>
    <App />
  </ApolloProvider> , $app); 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们必须调整我们的`Afterware`，以便每当服务器响应错误时，它调用传递的`onError`函数:

```
// graphql/index.js

import ApolloClient, { createNetworkInterface } from "react-apollo";

const createClient = ({ endpointUri: uri, onError }) => {
  const networkInterface = createNetworkInterface({ uri });

  networkInterface.useAfter([{
    applyAfterware({ response }, next) {
      if (response.status === 500) {
        return onError(new Error(`A fatal error occurred`));
      }

      next();
    }
  }]);

  return new ApolloClient({ networkInterface });
};

export { createClient }; 
```

Enter fullscreen mode Exit fullscreen mode

Wohoo！就是这样！从现在开始，只要出现错误，您的应用程序就会显示您的`<MyErrorHandler />`。任务完成！

如果我们可以使用 *React 16* 中引入的[错误边界](https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html)就太好了，但这是不可能的，因为 Apollo 客户端没有“抛出特性”(当您想要拥有细粒度的错误处理能力时，这是一件好事)。

这就是我现在要说的。希望你喜欢这个旅程，也许这个方法对你也有用:)

编码快乐！