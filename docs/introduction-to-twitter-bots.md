# Twitter 机器人简介

> 原文：<https://dev.to/codehakase/introduction-to-twitter-bots>

这是我第一次尝试制作推特机器人。我为这个博客做了一个非常简单的 twitter 机器人，检查了[来源](https://github.com/codehakase/hakasebot)，还关注了 [@_hakasebot](https://twitter.com/_hakasebot) 。

## 设置

机器人是使用 [Twit](https://github.com/ttezel/twit) 包创建的，这是 Node.js 的 Twitter API 客户端。Twit 需要与我的 Twitter 帐户连接，所以首先我创建了一个新的 [Twitter 应用程序](https://apps.twitter.com/)。之后，我记下了我的应用程序的键:

*   消费者密钥
*   消费者秘密
*   访问令牌
*   访问令牌秘密

您可以在应用仪表板的**键和访问令牌**面板中找到这些键。

一旦这些键都准备好了，我们就创建一个新的 Node.js 项目并初始化 Twit 包。

> 先决条件:Node.js、npm，当然还有一台 PC

所以你可以创建一个目录并创建三个文件`package.json`、`config.js`和`bot.js`

在`config.js`文件中，我们设置 Twit。

```
//config.js
const Twit = require('twit');
const TH = new Twit({ // Twit Handler
    consumer_key: APPLICATION_CONSUMER_KEY_HERE,
    consumer_secret: APPLICATION_CONSUMER_SECRET_HERE,
    access_token: ACCESS_TOKEN_HERE,
    access_token_secret: ACCESS_TOKEN_SECRET_HERE
}); 
```

Enter fullscreen mode Exit fullscreen mode

基本上,@_hakasebot 会执行以下操作:

*   监听事件和关键字
*   响应事件
    *   喜欢
    *   转发
    *   回答

## 监听事件和关键字

Twitter 有一个[流媒体 API](https://dev.twitter.com/streaming/overview) ，它让我们可以访问推文流。@_hakasebot 使用来自 API 的两个流:

*   **用户流**，这是对应于单个用户的推文流。
*   **公共流**，这是所有公共推文的流。

有了 *public* stream，@_hakasebot 可以监听来自任何用户的包含已定义关键字的推文。当我们基于使用**状态/过滤器**端点的过滤器创建一个 tweets 流，并传递一个带有过滤器参数的对象时，这是可能的。 **track** 参数用于按关键字过滤 tweets，它接受一个字符串或一组关键字来查找。

@_hakasebot 运行在一个过滤器上，该过滤器搜索这个博客的提及，所以我们实现如下:

```
const stream = TH.stream('statuses/filter', {
    track: ['hakasebot', 'hakaselabs', 'hakaselabs.github.io']
}); 
```

Enter fullscreen mode Exit fullscreen mode

当一个流打开时，我们可以监听并回复该流中的推文。

```
stream.on('tweet', (tweet) => {
    // We do something with that tweet here
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 响应事件

我们可以通过发布推文、转发、回复、关注用户等方式对事件做出回应。@_hakasebot 目前可以进行三种操作——赞、回复和转发。

### 喜欢一条推文

如果这条推文来自另一个账户，机器人会喜欢它。为了喜欢一条推文，我们发布到 **/favourites/create** 端点，传递要收藏的推文的 id。

```
stream.on('tweet', (tweet) => {
    if (tweet.user.id == _self.id) { // 
        // we'll get back to this 
    }
    TH.post('favourites/create', {
        id: tweet.id_str
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 回复推文

如果推文来自另一个用户，机器人会给他们发送回复。我们通过发布到**/status/update**端点并传递我们正在回复的 tweet 的 id 来发送回复。

```
stream.on('tweet', (tweet) => {
    if (tweet.user.id == _self.id) {....}

    TH.post('favourites/create', {
        id: tweet.id_str
    });
    TH.post('statuses/update', {
        status: `@${tweet.user.screen_name} Thanks for sharing :)`,
        in_reply_to_status_id: tweet.id_str 
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 转发

@_hakasebot 转发来自我的流的推文——这意味着如果从流中找到的推文来自我自己 [@codehakase](https://twitter.com/codehakase) ，它会转发它。我们可以通过发布到**/status/retweet/:id**端点来转发。

```
const _self = {
    id: 3354871743,
    screen_name: 'codehakase'
}
...

stream.on('tweet', (tweet) => {
    if (tweet.user.id == _self.id) {
        TH.post('statuses/retweet/:id', {
            id: tweet.id_str
        });
        return;
    }
    ....
}); 
```

Enter fullscreen mode Exit fullscreen mode

> 您可以从[这里](http://gettwitterid.com)获得您的 twitter 账户 id

## 部署 Bot

我用 [Heroku](http://heroku.com) 来主持@_hakasebot。由于这是一个 Node.js 应用程序，我们需要在我们的`package.json`文件中放置一些信息:

*   主脚本——文件 Node.js 将运行
*   属国
*   Node.js 版本

我的`package.json`文件是这样的:

```
{  "name":  "hakasebot",  "version":  "1.0.0",  "description":  "A twitter bot for hakaselabs.github.io",  "main":  "bot.js",  "scripts":  {  "start":  "node bot.js"  },  "author":  "Francis Sunday - codehakase",  "dependencies":  {  "twit":  "^2.2.5",  "express":  "^4.14.0"  },  "engines":  {  "node":  "7.9.0"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

太好了！你现在知道如何为 Twitter 制作机器人了。您可以关注 [@_hakasebot](https://twitter.com/_hakasebot) ，查看[来源](https://github.com/codehakase/hakasebot)，使用下面的 twitter 分享按钮进行测试。

你为推特建立过机器人吗？我想知道，把它们放在下面的评论区。

**原载于[我的博客](https://hakaselabs.github.io) -2017-05-30**