# 哈比神-er 快递路线与庆祝和 Joi

> 原文：<https://dev.to/dangolant/hapi-er-express-routing-with-celebrate-and-joi-76f>

我目前正在构建一个附带项目(GCP、Express、Vue 和一个 Chrome 扩展),我实际上想把它放在其他人面前，而不是仅仅用于我自己的实用程序，这对我来说很少见！这意味着我实际上需要构建健壮的错误处理和验证，因为我不再有一个非常慷慨的用户。质量工作的一个关键部分是验证传入 HTTP 请求中的数据是否存在，以及是否符合处理这些请求的路由的定义。

## 香草验证的陷阱

特别是在 Javascript 中，这种验证处理很容易成为代码整洁性的陷阱，在这种情况下，某些值的缺失或输入错误不容易通过使用类构造函数来处理。在另一种语言中，空值可以通过简单地将该值传递给给定路由最终将使用的任何类的构造函数来处理，如果构造函数未能返回对象，您可以用 400 错误代码拒绝请求。在 Javascript 中，您实际上没有相同的工具，因为这种语言的动态类型本质在这种情况下对您不利。这种天真的方法，也是我不幸地在过去至少遵循过一次的方法，是手动检查你的路由所期望的主体中的每个字段是否都存在。类似于:

```
 app.post('/user/links', function (req, res) {
        if (!req.body.important_value || !req.body.data1 || !req.body.data2) {
            logger.debug('USER UPDATE POST FAILED: MISSING VALUES', { 
                    request_body: req.body
                });
            res.status(400).send('Body Properties Missing: ' + req.body);
        }

        /* actual thing you want to do with this route */
    }); 
```

这种方法有什么问题？首先，这无疑会将人们的注意力从这条路线的实际功能上引开。在读者看到与路由操作相关的内容之前，他们已经(至少)往下看了六行。当您考虑到跨许多路由的这种逻辑的潜在重复时，即使是简单的路由也可能会变得非常大，尤其是当您考虑到我们在这种情况下只寻找三个值时。最重要的是，客户机没有得到多少关于有效负载中实际缺少什么期望值的信息。如果我们想提供更多的细节，另一个简单的方法可能是将这个逻辑分成多个条件:

```
 app.post('/linksforuser', function (req, res) {
        if (!req.body.important_value){
            logger.debug('USER UPDATE POST FAILED: MISSING IMPORTANT VALUE', {
                request_body: req.body
            })
            res.status(400).send('Body Important Value Missing: ' + req.body);

        }
        if(!req.body.data1) {
            logger.debug('USER UPDATE POST FAILED: MISSING DATA1 VALUE', {
                request_body: req.body
            })
            res.status(400).send('Body Properties Missing: ' + req.body);
        }
        if(!req.body.data2){
            logger.debug('USER UPDATE POST FAILED: MISSING DATA2 VALUE', {
                request_body: req.body
            })
            res.status(400).send('Body Properties Missing: ' + req.body);
        }
    }); 
```

完美，对吧？是的，您现在有了更准确的日志记录和响应消息，但是，与之前的 6 行相比，您增加了 18 行验证。最重要的是，也许我是唯一一个这样做的人，但是复制和粘贴日志消息通常会在某些时候伤害到我。我总是复制并粘贴一条消息，之后不进行更新，最终当出现问题时，我会尝试调试一个完全不同的行或文件。另外，这个有效负载仍然相当小，随着它的增长，您的验证也会增长。为了解决这个问题，你可以尝试将你的整个有效负载包装在一个对象中，但是你会遇到比较对象键的问题，我们甚至还没有解决属性的实际**值**。

那么 dev 应该做些什么呢？我们可以添加一行又一行脆弱的验证逻辑，或者我们可以编写一个包罗万象的函数，每次我们的有效负载发生变化时，我们都必须重新工具化，对吗？幸运的是，情况不一定如此。

[![wrong](img/9166bd1f264fb53182d907bb3818ef6f.png)T2】](https://i.giphy.com/media/ceeN6U57leAhi/giphy.gif)

## 验证模块

你看，Express 为我们提供了预制的中间件模块，这些模块——就像你自己编写的任何中间件一样——可以很容易地操纵路由的请求和响应对象。如果您愿意，您可以将所有的验证逻辑作为定制的中间件函数，以便至少将所有这些东西隐藏起来。但是你为什么想要呢？有许多预制的、健壮的、经过良好测试的快速请求验证模块。

在使用了其中的一些模块后，我觉得有些地方不太对劲。如果他们应该清理我的代码，那么我觉得他们做得还不够。我最终还是会得到我不太满意的消息，或者不适合我的验证格式。那当然都是品味。

## Joi 和庆典

这时我发现了 Joi，它是哈比神的内置模式验证库(另一个类似 Express 的节点框架)。我突然明白了，Joi 正是我一直在寻找的。一旦我找到 Joi，我只用了很短的时间就发现了[庆祝](https://github.com/continuationlabs/celebrate)，一个用于 Joi 验证的快速中间件。你可以在这里读到作者庆祝[背后的意图和推理，我觉得这很有说服力，也很有思想。我通常更倾向于那些花时间详细描述、论证和分享他们的决策的人编写的工具，因为这让我更有信心，他们已经全面考虑了这个主题。](https://blog.continuation.io/time-to-celebrate/)

pressure 允许用户利用 Joi 简单而强大的对象定义和验证功能，只需一行代码**就可以向客户端返回信息性的错误消息，缩短验证失败的请求，并允许对象验证器在需要处理传入请求时更新请求对象本身。它也非常容易上手。**

## 用 Joi 与庆功

首先，一如既往:

```
 npm install celebrate 
```

然后，无论何时你需要使用庆祝和 Joi，只需添加下面几行

```
 const express = require('express');
    const BodyParser = require('body-parser');
    const Celebrate = require('celebrate');
    const { Joi } = Celebrate;

    const app = express(); // use whatever name you like, I tend to default to app
    app.use(BodyParser.json()); 
```

要验证该文件中的路由，您可以使用类似如下的内容来定义您的路由:

```
 app.post('/user/links', Celebrate({
      body: Joi.object().keys({
        important_value: Joi.string().required(), // look, type enforcement!
        data1: Joi.number().integer(),
        data2: Joi.string().default('admin') // hey, and defaults!
      }),
      query: {
        token: Joi.string().token().required() // you can use one object to 
                                               // validate body, query, 
                                               // and params all at once
      }
    }), (req, res) => {
        /* Here we do whatever the route is 
        actually supposed to do, 
        because Celebrate will automatically res.status(400).send() 
        an informative message 
        if the validation fails 
        */
    });
    app.use(Celebrate.errors());

    // taken and modified from the Celebrate docs 
```

哇，看那个！我们现在有了类型强制和缺省值，比以前简单的验证多得多，而且只用了 8 行代码！

"但是等等，丹，你不是说**一行代码吗？"**

[![Wait a minute!](img/303c4ae3fb0c93eb2094228d555420c4.png)T2】](https://i.giphy.com/media/VStxBrCyssRPO/giphy.gif)

## 我的实现

嗯，这是一行*庆祝*代码，显然任何对象定义的大小都将取决于被验证对象的大小，更不用说 LOC 是一个任意的度量，但我们实际上可以将它压缩得更多一点。我处理验证的方式如下所示:

```
 const SCHEMA_POST_LINKS = require('./request_schemas/link_collection_routes/links_POST_schema.js');

    app.post('/user/links', Celebrate({
        body: SCHEMA_POST_LINKS
    }), (req, res) => {
        logger.info('POST received... \tCreateUser'); // hey look, 
                                                      // a logging mistake I just 
                                                      // discovered because 
                                                      // I forgot to change what I c/p'd 
                                                      // (I am not kidding);

        // Here we add some links to a user object, 
        // knowing that our validation already happened
    }); 
```

我们现在已经将我们的请求模式分解到一个不同的文件中，只剩下一行验证(正如承诺的那样😁).请求模式如下所示:

```
 const { Joi } = require('celebrate');

    const links_POST_schema = Joi.object().keys({
        access_token: Joi.string(),
        id_token: Joi.string(),
        url: Joi.string().required(),
        title: Joi.string().required()
    }).xor('access_token', 'id_token');

    module.exports = links_POST_schema; 
```

看到那个`xor`条件了吗？这是一个漂亮的小便利，如果手动添加的话会很烦人。它允许我绕过我正在使用的 API 的限制，而不用复制我的路线，通过强制只有一个`access_token`或一个`id_token`可以出现在有效载荷中，而不是两个都出现。最重要的是，因为 pressure 包含了自己正式的 Joi 依赖项，我们可以使用 Joi 的一致版本来验证除 HTTP 请求之外的其他对象(比如响应)。下面是我对服务器以相同路径发送的`response`的验证，它增加了一层保护，防止发送难看的错误:

```
 Joi.validate(userEntity, SCHEMA_RES_LINKS).then((userEntity) => {
        res.send(userEntity);
    }).catch((reason) => res.status(400).send(`Something appears to be wrong with this account: ${reason}`)); 
```

Joi 提供了大量非常简洁、有用的工具来验证和自动转换传入的请求数据， [API 文档也很棒](https://github.com/hapijs/joi/blob/v13.0.1/API.md)。prosperity 是一个自包含的可读中间件包装器，它以一种智能的方式利用 Joi，并使更新带有信息错误的路线变得轻而易举。当我开始使用它们时，老实说，我对我的路线有多顺畅感到**眩晕**。

PS:如果你想看看我目前在做什么，请点击 [LinkMeLater](https://tabmailer-174400.appspot.com/signup#/) ！它仍在测试中，所以期待收到我的一些电子邮件😁