# 介绍 Grial

> 原文：<https://dev.to/sergiodxa/introducing-grial>

## 用 Node.js 轻松创建 GraphQL API

> 一个 Node.js 框架，用于轻松创建 GraphQL API 服务器，并且没有大量样板文件。

任何 GraphQL API 的核心服务器代码基本上都是一样的，创建 HTTP 服务器，获取解析器和模式，设置`/graphql`端点，在开发阶段为 GraphQL 设置端点，如果您打算使用订阅，还需要创建一个订阅服务器并将其附加到 HTTP 服务器。

那是一大堆样板代码！和 [Grial](https://github.com/sergiodxa/grial) 将为您处理所有这些，让您只考虑您的应用程序业务逻辑和 API 层。怎么会？Grial 这是一系列的小库，核心是 [@grial/cli](https://www.npmjs.com/package/@grial/cli) 和 [@grial/server](https://www.npmjs.com/package/@grial/server) ，它们可以创建你的 API 服务器，并通过一个命令运行它。

Grial】还有 [多个](https://www.npmjs.com/package/@grial/connector-faker) [连接器](https://www.npmjs.com/package/@grial/connector-fs) [小](https://www.npmjs.com/package/@grial/connector-mongodb) [库](https://www.npmjs.com/package/@grial/connector-redis)允许你轻松地将你的 API 连接到不同的数据源，并允许创建一个大的 API，从 Redis、MongoDB、Rest APIs、file disk 等使用数据。

## 我们来编码一个 API 吧！

编码时间到了，我们将为 API 创建一个目录并安装这个模块。

```
yarn add @grial/cli @grial/server @grial/connector-faker 
```

Enter fullscreen mode Exit fullscreen mode

最后一个是 faker 模块的连接器，我们将使用它来伪造 API 数据。然后我们将把下面的脚本添加到我们的`package.json`文件中。

```
{
  "scripts": {
    "start": "grial start"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

该脚本将使用`@grial/cli`来运行我们的`@grial/server`，它还将尝试从`.env`文件中读取我们的环境变量，然后我们可以用这些基本变量创建一个文件。

```
PORT=8000 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下它会设置`PORT=3000`，每个 Grial 模块在 readme 中都有必需的和可能的环境变量。

### 定义模式

在那之后，我们准备写我们的 API 代码，让我们定义我们的模式，Grial 让我们把它写在一个`schema.gql`或`schema.graphql`文件中，所以我们将在其中一个文件中定义类似这样的东西。

```
type  User  {  id:  Int!  username:  String!  firstName:  String!  lastName:  String!  fullName:  String!  bio:  String!  }  type  Query  {  me:  User!  }  schema  {  query:  Query  } 
```

Enter fullscreen mode Exit fullscreen mode

### 写出解析器

现在我们需要创建一个`resolvers.js`文件。除了模式之外，这些是唯一需要的文件。

```
// main resolvers
exports.Query = {
  me(rootQuery, args, context) {
    return context.models.User.me();
  }
};
// type resolvers
exports.User = {
  fullName(user) {
    return `${user.firstName}  ${user.lastName}`;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们导出了许多键，但是我们也可以使用`module.exports`只导出一个带有键 Query 和 User 的对象。

> 我个人的建议是使用多种导出，并且随着解析器的增长，创建一个包含文件`Query.js`、`Mutation.js`、`Subscription.js`的`resolvers`目录，并且为我们需要的每种类型创建一个自定义解析器。

### 创建模型

正如我们看到的，我们从我们的`context`对象接收了模型`User`，Grial 将自动读取我们的`models.js`文件并实例化每一个。我们现在将创建这个文件。

```
exports.User = async function User({ faker }) {
  return {
    me() {
      return {
        id: faker.random.number(),
        username: faker.internet.userName(),
        firstName: faker.name.firstName(),
        lastName: faker.name.lastName(),
        bio: faker.name.title()
      };
    }
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的`User`模型，我们再次使用了名称导出，但我们也可以用一个对象进行单次导出。如果您检查我们正在为我们的模型创建一个异步函数，那是因为我们可能需要在创建我们的解析器之前运行异步代码(也许是为了将模型与数据库同步)。

### 导入连接器

我们还在我们的**模型**中接收了`faker`，那是我们的**连接器**，每个模型接收一个带有每个环境变量和连接器的单个参数，这当然允许单个模型使用多个连接器获取数据。

我们收到了连接器，但是 Grial 是怎么知道的呢？很简单，只需创建一个`connectors.js`对象，并导出每个想要使用的连接器。

```
exports.faker = require('@grial/connector-faker'); 
```

Enter fullscreen mode Exit fullscreen mode

这些是我们的 API 连接器，我们可以使用这个文件来定义自定义连接器，可能使用第三方 API 客户端。每个连接器都将接收每个环境变量，并且它将使用其中的一些变量，Faker 连接器使用`FAKER_LOCALE`和`FAKER_SEED`并有默认值。

如果你想多次使用同一个连接器，你可以用一个高阶函数来包装它们，这个函数接收环境变量并传递新的变量。

```
exports.faker = env => require('@grial/connector-faker')({
  FAKER_LOCALE: env.DATA_LOCALE,
  FAKER_SEED: env.DATA_SEED
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 运行 app

完成后，我们已经准备好 API 代码，只需运行`yarn start`或`npm start`，您将在终端中看到类似这样的内容。

```
$ grial start
Grial server running
> GraphiQL Endpoint      = http://localhost:8000/ide
> API Endpoint           = http://localhost:8000/graphql
> Subscriptions Endpoint = http://localhost:8000/subscriptions 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以访问`http://localhost:3000/ide`并尝试 API。如您所见，Grial 还为您默认设置了一个订阅端点，如果您创建了一个 PubSub 实例并将订阅添加到您的模式和解析器中，您就可以放心地开始使用它们了。

您还可以尝试运行在生产环境中的这个应用程序访问[https://grial-example-basic.now.sh/ide](https://grial-example-basic.now.sh/ide)和另一个用 Grial 构建的 API，从 Rest API[https://grial-example-rest-wrapper.now.sh/ide](https://grial-example-rest-wrapper.now.sh/ide)获取数据。

第二个示例将 [JSONPlaceholder API](http://jsonplaceholder.typicode.com/) 包装到 GraphQL 中，并为模型和解析器添加测试。

## 最后的话

Grial 允许[使用带有密钥`graphqlConfig`、`graphiqlConfig`和`subscriptionConfig`的`grial.config.js`文件自定义其行为](https://github.com/sergiodxa/grial/wiki/Custom-config-with-grial.config.js)，它们允许您自定义 GraphQL、GraphiQL 和订阅服务器配置，有助于包括一些基于令牌的认证和其他功能。

它也是可插拔的，你可以使用[编程 API](https://github.com/sergiodxa/grial/wiki/Programmatically-usage) 将其集成到任何 HTTP 服务器或应用内部，它可以是 [Next.js](https://github.com/sergiodxa/grial/wiki/Integration-with-Next.js) 、 [Express.js](https://github.com/sergiodxa/grial/wiki/Integration-with-Express) 等。

你可以[为 Grial 贡献](https://github.com/sergiodxa/grial)并帮助创建一个有更多[连接器](https://github.com/sergiodxa/grial/wiki/Creating-a-connector)、特性、工具、[示例](https://github.com/sergiodxa/grial/tree/master/examples)和[完整文档](https://github.com/sergiodxa/grial/wiki)的出色 GraphQL 框架。

[![Subscribe to learn when I publish new essays on https://sergiodxa.com/subscribe/](img/7e68c2ede6ebb6eea7da7724bdd9d783.png)T2】](https://sergiodxa.com/subscribe/)