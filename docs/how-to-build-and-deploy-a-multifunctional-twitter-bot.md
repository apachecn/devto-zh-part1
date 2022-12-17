# 如何构建和部署多功能 Twitter 机器人

> 原文：<https://dev.to/spences10/how-to-build-and-deploy-a-multifunctional-twitter-bot>

我又忙着造推特机器人了！

如果你看一下我的 [GitHub 简介](https://github.com/spences10?utf8=%E2%9C%93&tab=repositories&q=twitt&type=&language=)，你会发现我有很多关于 Twitter 机器人的回复。

我最近的项目是从决定将我的一个测试回购重新定位为如何使用 npm `twit`包的文档开始的。但是当我添加新的例子时，它很快就变成了另一个 Twitter 机器人。

这个机器人是由三个例子拼凑而成的，我们将在这里介绍。我还会详细说明我是如何使用 Zeit 的 now 平台将机器人部署到服务器上的。

特别感谢 Tim 帮助我完成了 now 部署。还有汉娜·戴维斯的 egghead.io 课程资料。

它有一些非常简洁的例子，我已经在相关章节中链接了这些例子。

## 入门

这是我和其他任何对 JavaScript 中的 Twitter 机器人感兴趣的人的参考。

这里所有的例子都使用了 [`npm`](https://www.npmjs.com/) 包 [`twit`](https://www.npmjs.com/package/twit) 。

我们将通过设置一个简单的机器人来运行这些例子。

我将假设您已经安装了`nodejs`和`npm`,并且您对终端感到满意。

如果你不熟悉 node 或者没有设置使用它的环境，看看我的 [Twitter bot bootstrap](https://github.com/spences10/twitter-bot-bootstrap) repo 上的 [README.md](https://github.com/spences10/twitter-bot-bootstrap#twitter-bot-bootstrap) ，它详细描述了如何设置 Twitter 应用程序和使用 c9 的开发环境。

Aman Mittal 的 [令人敬畏的 Twitter 机器人](https://github.com/amandeepmittal/awesome-twitter-bots) repo 是一个很好的资源，其中有资源和机器人的例子。

也可以在 Gitter 上聊天: [![Chat](img/1ee6f8d0143863677e9b8bed295a064f.png)](https://gitter.im/awesome-twitter-bots/Lobby?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

已经有很多这样的信息了，我希望这是一些人开始使用他们自己的 Twitter 机器人所需要的全部信息。我这样做是为了我自己的学习，希望其他人也能从中有所收获。

## 设置 bot

在触摸终端或编写任何代码之前，我们需要创建一个 [Twitter 应用程序](https://apps.twitter.com/app/new)来获取我们的 API 密钥，我们需要它们全部:

```
Consumer Key (API Key)
Consumer Secret (API Secret)
Access Token
Access Token Secret 
```

Enter fullscreen mode Exit fullscreen mode

请将密钥保存在安全的地方，以便您在需要时可以再次使用它们，我们将在将要创建的 [`.env`](https://www.npmjs.com/package/dotenv) 文件中使用它们。

我们使用 [`dotenv`](https://www.npmjs.com/package/dotenv) 这样，如果将来我们想把我们的机器人添加到 GitHub，Twitter API 密匙不会添加到 GitHub 让所有人看到。

从头开始，通过终端创建一个新文件夹，并通过`npm`或`yarn`初始化`package.json`,对于所有这些例子，我们都需要`twit`和`dotenv`。

对于所有这些例子，我将使用`yarn`，如果你愿意，你也可以使用`npm`。

终端命令:

```
mkdir tweebot-play
cd tweebot-play
yarn init -y
yarn add twit dotenv
touch .env .gitignore index.js 
```

Enter fullscreen mode Exit fullscreen mode

如果你看一看被创建的`package.json`，它应该是这样的:

```
{  "name":  "tweebot-play",  "version":  "1.0.0",  "main":  "index.js",  "author":  "Scott Spence <spences10apps@gmail.com> (https://spences10.github.io/)",  "license":  "MIT",  "dependencies":  {  "dotenv":  "^4.0.0",  "twit":  "^2.2.5"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

在`package.json`中添加一个`npm`脚本，在我们测试和寻找输出时启动机器人:

```
 "scripts":  {  "start":  "node index.js"  }, 
```

Enter fullscreen mode Exit fullscreen mode

现在应该是这样的:

```
{  "name":  "tweebot-play",  "version":  "1.0.0",  "main":  "index.js",  "scripts":  {  "start":  "node index.js"  },  "author":  "Scott Spence <spences10apps@gmail.com> (https://spences10.github.io/)",  "license":  "MIT",  "dependencies":  {  "dotenv":  "^4.0.0",  "twit":  "^2.2.5"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在`index.js`中添加以下指向机器人的指针，就像这样:

```
require('./src/bot') 
```

Enter fullscreen mode Exit fullscreen mode

所以当我们使用`yarn start`运行机器人时，它调用`index.js`文件，后者运行我们将要创建的`src`文件夹中的`bot.js`文件。

现在我们将我们的 API 键添加到`.env`文件中，它应该看起来像这样:

```
CONSUMER_KEY=AmMSbxxxxxxxxxxNh4BcdMhxg
CONSUMER_SECRET=eQUfMrHbtlxxxxxxxxxxkFNNj1H107xxxxxxxxxx6CZH0fjymV
ACCESS_TOKEN=7xxxxx492-uEcacdl7HJxxxxxxxxxxecKpi90bFhdsGG2N7iII
ACCESS_TOKEN_SECRET=77vGPTt20xxxxxxxxxxxZAU8wxxxxxxxxxx0PhOo43cGO 
```

Enter fullscreen mode Exit fullscreen mode

在`.gitignore`文件中我们需要添加`.env`和`node_modules`T3

```
# Dependency directories
node_modules

# env files
.env 
```

Enter fullscreen mode Exit fullscreen mode

然后初始化 git:

```
git init 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在我们可以开始配置机器人了，我们需要一个`src`文件夹一个`bot.js`文件和一个`config.js`文件。

终端:

```
mkdir src
cd src
touch config.js bot.js 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以设置 bot 配置，打开`config.js`文件并添加以下内容:

```
require('dotenv').config()

module.exports = {
  consumer_key: process.env.CONSUMER_KEY,
  consumer_secret: process.env.CONSUMER_SECRET,
  access_token: process.env.ACCESS_TOKEN,
  access_token_secret: process.env.ACCESS_TOKEN_SECRET,
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，这就是机器人的配置，现在我们可以设置机器人了，这里详述的每个例子都有相同的三行代码:

```
const Twit = require('twit')
const config = require('./config')

const bot = new Twit(config) 
```

Enter fullscreen mode Exit fullscreen mode

好了，就这样，bot 准备好了，从终端用`yarn start`做一个测试，我们应该得到这个输出:

```
yarn start
yarn start v0.23.4
$ node index.js
Done in 0.64s. 
```

Enter fullscreen mode Exit fullscreen mode

Bot 现在已经配置好了，可以开始了！ðŸš€

## 发布状态

首先发布状态，用`.post('statuses/update'...` bot 会发布一个 hello world！状态。

```
bot.post('statuses/update', {
  status: 'hello world!'
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    console.log(`${data.text} tweeted!`)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 与用户一起工作

要获得关注者 id 列表，请使用`.get('followers/ids'...`并包括您想要关注者的帐户，在本例中我们使用 [`@DroidScott`](https://twitter.com/DroidScott) ，您可以使用任何您喜欢的帐户。在本例中，我们可以将它们注销到控制台。

```
bot.get('followers/ids', {
  screen_name: 'DroidScott',
  count: 5
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    console.log(data)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

您可以用`count`参数指定一次得到多少个结果，最多 100 个。

或者使用`.get('followers/list'...`获取详细列表

在这里，我们打印出一份清单，列出每次通话最多 200 次的`user.screen_name`。

```
bot.get('followers/list', {
  screen_name: 'DroidScott',
  count:200
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    data.users.forEach(user => {
      console.log(user.screen_name)
    })
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

要跟踪一个追随者，我们可以使用`.post('friendships/create'...`这里机器人正在跟踪用户`MarcGuberti`

> 机器人应该只关注关注机器人的用户。

```
bot.post('friendships/create', {
  screen_name: 'MarcGuberti'
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    console.log(data)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

像关注者一样，你可以获得你的机器人正在关注的账户列表。

```
bot.get('friends/ids', {
  screen_name: 'DroidScott'
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    console.log(data)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

还有一份详细的清单。

```
bot.get('friends/list', {
  screen_name: 'DroidScott'
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    console.log(data)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

获得友谊状态，这对于追随新的追随者是有用的，这将给我们一个特定用户的关系。因此，您可以浏览您的关注者列表，跟踪任何没有`following`连接的用户。

让我们看看我们的机器人和 [`@ScottDevTweets`](https://twitter.com/ScottDevTweets)
之间的关系

```
bot.get('friendships/lookup', {
  screen_name: 'ScottDevTweets'
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    console.log(data)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果用户跟随机器人，那么关系将是:

```
[ { name: 'Scott Spence ðŸŒ¯ðŸ˜´ðŸ’»â™»',
    screen_name: 'ScottDevTweets',
    id: 4897735439,
    id_str: '4897735439',
    connections: [ 'followed_by' ] } ] 
```

Enter fullscreen mode Exit fullscreen mode

如果用户和机器人相互跟随，关系将是:

```
[ { name: 'Scott Spence ðŸŒ¯ðŸ˜´ðŸ’»â™»',
    screen_name: 'ScottDevTweets',
    id: 4897735439,
    id_str: '4897735439',
    connections: [ 'following', 'followed_by' ] } ] 
```

Enter fullscreen mode Exit fullscreen mode

如果没有关系，那么:

```
[ { name: 'Scott Spence ðŸŒ¯ðŸ˜´ðŸ’»â™»',
    screen_name: 'ScottDevTweets',
    id: 4897735439,
    id_str: '4897735439',
    connections: [ 'none' ] } ] 
```

Enter fullscreen mode Exit fullscreen mode

用`bot.post('direct_messages/new'...`直接给用户发消息

> 一个机器人应该只 DM 一个跟随机器人帐户
> 的用户

```
bot.post('direct_messages/new', {
  screen_name: 'ScottDevTweets',
  text: 'Hello from bot!'
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    console.log(data)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 与推文互动

使用`.get(statuses/home_timeline'...`
获取机器人时间线中的推文列表

```
bot.get('statuses/home_timeline', {
  count: 1
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    console.log(data)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

为了更加细化，你可以提取每条推文的具体信息。

```
bot.get('statuses/home_timeline', {
  count: 5
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    data.forEach(t => {
      console.log(t.text)
      console.log(t.user.screen_name)
      console.log(t.id_str)
      console.log('\n')
    })
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

要转发，使用`.post('statuses/retweet/:id'...`并传递一个 tweet id 给 retweet。

```
bot.post('statuses/retweet/:id', {
  id: '860828247944253440'
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    console.log(`${data.text} retweet success!`)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

要取消转发，只需使用`.post('statuses/unretweet/:id'...`

```
bot.post('statuses/unretweet/:id', {
  id: '860828247944253440'
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    console.log(`${data.text} unretweet success!`)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

要喜欢一条推文，请使用`.post('favorites/create'...`

```
bot.post('favorites/create', {
  id: '860897020726435840'
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    console.log(`${data.text} tweet liked!`)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

要想不像一个帖子使用`.post('favorites/destroy'...`

```
bot.post('favorites/destroy', {
  id: '860897020726435840'
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    console.log(`${data.text} tweet unliked!`)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

回复一条推文和发布一条推文非常相似，但是你需要包含`in_reply_to_status_id`参数，但是这还不够，因为你还需要输入你回复的人的用户名。

```
bot.post('statuses/update', {
  status: '@ScottDevTweets I reply to you yes!',
  in_reply_to_status_id: '860900406381211649'
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    console.log(`${data.text} tweeted!`)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

最后，如果你想删除一条推文，使用`.post('statuses/destroy/:id'...`传递你想删除的推文 id。

```
bot.post('statuses/destroy/:id', {
  id: '860900437993676801'
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    console.log(`${data.text} tweet deleted!`)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 Twitter 搜索

要使用 search use `.get('search/tweets',...`进行搜索，有很多搜索参数。

`q: ''`Q 代表查询，所以使用`q: 'mango'`来搜索 mango，我们也可以使用`count: n`来限制返回的结果，所以让我们在示例中将其计数限制为:

```
bot.get('search/tweets', {
  q: 'mango',
  count: 5
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    console.log(data.statuses)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

就像我们对时间线所做的那样，我们将从返回的`data.statuses`中提取特定的项目，就像这样:

```
bot.get('search/tweets', {
  q: 'mango',
  count: 5
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    data.statuses.forEach(s => {
      console.log(s.text)
      console.log(s.user.screen_name)
      console.log('\n')
    })
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

搜索 API 返回的是相关性而不是完整性，如果你想搜索一个精确的短语，你需要用引号将查询括起来`"purple pancakes"`如果你想搜索两个单词中的一个，那么使用`OR`如`'tabs OR spaces'`如果你想搜索两个，使用`AND`如`'tabs AND spaces'`。

如果你想搜索一条没有其他单词的推文，使用`-`比如`donald -trump`，你也可以多次使用，比如`donald -trump -duck`

可以用表情符号搜索推文，喜欢的`q: 'sad :('`试试看！

当然要寻找标签。查找发给用户`q: 'to:@stephenfry'`或来自用户`q: 'from:@stephenfry'`的推文

你可以用参数`filter:safe`过滤掉不雅的推文，你也可以用它过滤掉包含视频的推文。您可以指定`images`查看带有图片的推文，也可以指定`links`查看带有链接的推文。

如果你想要某个网站的推文，你可以用`url:asda`
这样的`url`参数来指定

```
bot.get('search/tweets', {
  q: 'from:@dan_abramov url:facebook filter:images since:2017-01-01',
  count: 5
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    data.statuses.forEach(s => {
      console.log(s.text)
      console.log(s.user.screen_name)
      console.log('\n')
    })
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在是最后几个，有一个将返回`recent`、`popular`或`mixed`结果的`result_type`参数。

`geocode`参数，格式为纬度经度，然后是英里半径`'51.5033640,-0.1276250,1mi'`示例:

```
bot.get('search/tweets', {
  q: 'bacon',
  geocode: '51.5033640,-0.1276250,1mi',
  count: 5
}, (err, data, response) => {
  if (err) {
    console.log(err)
  } else {
    data.statuses.forEach(s => {
      console.log(s.text)
      console.log(s.user.screen_name)
      console.log('\n')
    })
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 Twitter 流 API

使用流 API 有两种方法，首先是`.stream('statuses/sample')`示例:

```
const stream = bot.stream('statuses/sample');

stream.on('tweet', t => {
  console.log(`${t.text}\n`)
}) 
```

Enter fullscreen mode Exit fullscreen mode

这将给你一个随机的 tweets 样本。

对于更具体的信息，使用`.stream('statuses/filter')...`然后传递一些参数，使用`track:`指定一个搜索字符串:

```
var stream = bot.stream('statuses/filter', {
  track: 'bot'
})

stream.on('tweet', function (t) {
  console.log(t.text + '\n')
}) 
```

Enter fullscreen mode Exit fullscreen mode

您也可以在`track`参数中使用多个单词，这会得到包含`twitter`或`bot`的结果。

```
const stream = bot.stream('statuses/filter', {
  track: 'twitter, bot'
});

stream.on('tweet', t => {
  console.log(`${t.text}\n`)
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果你想要两个单词，那么去掉逗号`,`，你可以把空格想成`AND`，逗号想成`OR`

您也可以使用`follow:`参数来输入特定用户的 id，例如:

```
const stream = bot.stream('statuses/filter', {
  follow: '4897735439'
});

stream.on('tweet', t => {
  console.log(`${t.text}\n`)
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 推文媒体文件

感谢[汉娜·戴维斯](https://egghead.io/instructors/hannah-davis)精彩的内容，这个 [egghead.io](https://egghead.io/lessons/node-js-tweet-media-files-with-twit-js) 视频是这一部分的重要资源！

这将是一个获取 [NASA 当日图像](https://www.nasa.gov/multimedia/imagegallery/iotd.html)并发布的请求。

为此，我们需要引用`request`和`fs`来处理文件系统。

```
const Twit = require('twit')
const request = require('request')
const fs = require('fs')
const config = require('./config')

const bot = new Twit(config) 
```

Enter fullscreen mode Exit fullscreen mode

首先从 NASA api 获取照片，为此我们需要在我们的`getPhoto`函数中创建一个参数对象，该对象将被传递给节点 HTTP 客户端`request`以获取照片:

```
function getPhoto() {
  const parameters = {
    url: 'https://api.nasa.gov/planetary/apod',
    qs: {
      api_key: process.env.NASA_KEY
    },
    encoding: 'binary'
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

`parameters`为此指定一个`api_key`你可以[申请一个 API 密匙](https://api.nasa.gov/index.html#apply-for-an-api-key)或者你可以使用`DEMO_KEY`这个 API 密匙可以用来在注册之前初步探索 API，但是它有低得多的速率限制，所以鼓励你注册自己的 API 密匙。

在这个例子中，你可以看到我已经用其余的`.env`变量配置了我的键。

```
CONSUMER_KEY=AmMSbxxxxxxxxxxNh4BcdMhxg
CONSUMER_SECRET=eQUfMrHbtlxxxxxxxxxxkFNNj1H107xxxxxxxxxx6CZH0fjymV
ACCESS_TOKEN=7xxxxx492-uEcacdl7HJxxxxxxxxxxecKpi90bFhdsGG2N7iII
ACCESS_TOKEN_SECRET=77vGPTt20xxxxxxxxxxxZAU8wxxxxxxxxxx0PhOo43cGO

NASA_KEY=DEMO_KEY 
```

Enter fullscreen mode Exit fullscreen mode

现在使用`request`来获取图像:

```
function getPhoto() {
  const parameters = {
    url: 'https://api.nasa.gov/planetary/apod',
    qs: {
      api_key: process.env.NASA_KEY
    },
    encoding: 'binary'
  };
  request.get(parameters, (err, respone, body) => {
    body = JSON.parse(body)
    saveFile(body, 'nasa.jpg')
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

在`request`中，我们传入参数，并把主体解析为 JOSN，这样我们就可以用`saveFile`函数保存它，我们现在来看看:

```
function saveFile(body, fileName) {
  const file = fs.createWriteStream(fileName);
  request(body).pipe(file).on('close', err => {
    if (err) {
      console.log(err)
    } else {
      console.log('Media saved!')
      console.log(body)
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

`request(body).pipe(file).on('close'...`保存来自`file`变量的文件，变量的名字从`getPhoto`函数传递给变量`nasa.jpg`。

调用`getPhoto()`将会把 NASA 当天的图像保存到你的项目的根目录中。

现在我们可以在推特ðÿ˜ž上分享它

这包括两个部分，首先保存文件。

```
function saveFile(body, fileName) {
  const file = fs.createWriteStream(fileName);
  request(body).pipe(file).on('close', err => {
    if (err) {
      console.log(err)
    } else {
      console.log('Media saved!')
      const descriptionText = body.title;
      uploadMedia(descriptionText, fileName)
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

然后`uploadMedia`在我们可以发布之前将媒体上传到 Twitter，这让我有点困惑，因为我的文件在`src`文件夹中，如果你的 bot 文件嵌套在文件夹中，那么如果你正在与`file does not exist`错误作斗争，你也需要这样做:

给`path`加一个`require`，然后使用带有相关相对文件路径的`join`。

```
const path = require('path')
//...
const filePath = path.join(__dirname, '../' + fileName) 
```

Enter fullscreen mode Exit fullscreen mode

在此完成功能:

```
function uploadMedia(descriptionText, fileName) {
  console.log(`uploadMedia: file PATH ${fileName}`)
  bot.postMediaChunked({
    file_path: fileName
  }, (err, data, respone) => {
    if (err) {
      console.log(err)
    } else {
      console.log(data)
      const params = {
        status: descriptionText,
        media_ids: data.media_id_string
      }
      postStatus(params)
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

然后用我们在`uploadMedia`中创建的`params`，我们可以用一个简单的`.post('statuses/update'...`
发布

```
function postStatus(params) {
  bot.post('statuses/update', params, (err, data, respone) => {
    if (err) {
      console.log(err)
    } else {
      console.log('Status posted!')
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

调用`getPhoto()`函数置顶发布到 Twitter...超级直截了当，对不对，ðÿ˜€不，我知道不是。下面是完整的模块:

```
const Twit = require('twit')
const request = require('request')
const fs = require('fs')
const config = require('./config')
const path = require('path')

const bot = new Twit(config)

function getPhoto() {
  const parameters = {
    url: 'https://api.nasa.gov/planetary/apod',
    qs: {
      api_key: process.env.NASA_KEY
    },
    encoding: 'binary'
  }
  request.get(parameters, (err, respone, body) => {
    body = JSON.parse(body)
    saveFile(body, 'nasa.jpg')
  })
}

function saveFile(body, fileName) {
  const file = fs.createWriteStream(fileName)
  request(body).pipe(file).on('close', err => {
    if (err) {
      console.log(err)
    } else {
      console.log('Media saved!')
      const descriptionText = body.title
      uploadMedia(descriptionText, fileName)
    }
  })
}

function uploadMedia(descriptionText, fileName) {
  const filePath = path.join(__dirname, `../${fileName}`)
  console.log(`file PATH ${filePath}`)
  bot.postMediaChunked({
    file_path: filePath
  }, (err, data, respone) => {
    if (err) {
      console.log(err)
    } else {
      console.log(data)
      const params = {
        status: descriptionText,
        media_ids: data.media_id_string
      }
      postStatus(params)
    }
  })
}

function postStatus(params) {
  bot.post('statuses/update', params, (err, data, respone) => {
    if (err) {
      console.log(err)
    } else {
      console.log('Status posted!')
    }
  })
}

getPhoto() 
```

Enter fullscreen mode Exit fullscreen mode

## 制作马氏机器人

这是相当整洁的，再次从 [egghead.io](https://egghead.io/lessons/node-js-make-a-bot-that-sounds-like-you-with-rita-js?series=create-your-own-twitter-bots) 系列它使用 [`rita`](https://www.npmjs.com/package/rita) 自然语言工具包。它还使用了`csv-parse`,因为我们将读取我们的 Twitter 档案，使机器人听起来像我们在发推文。

首先，要设置 [Twitter 存档](https://support.twitter.com/articles/20170160)，你需要从 Twitter 设置页面请求你的数据。你会收到一封电子邮件，里面有下载你的档案的链接，然后当你下载完档案后，提取出`tweets.csv`文件，我们会把它放在它自己的文件夹里，所以从你的项目的根目录:

```
cd src
mkdir twitter-archive 
```

Enter fullscreen mode Exit fullscreen mode

我们将把我们的`tweets.csv`移到那里，让我们现在要学习的机器人访问它。

使用`fs`设置读取流...

```
const filePath = path.join(__dirname, './twitter-archive/tweets.csv')

const tweetData =
  fs.createReadStream(filePath)
  .pipe(csvparse({
    delimiter: ','
  }))
  .on('data', row => {
    console.log(row[5])
  }) 
```

Enter fullscreen mode Exit fullscreen mode

当您从控制台运行这个程序时，您应该会从您的 Twitter 存档中获得输出。

现在清除掉像`@`和`RT`这样的东西来帮助自然语言处理，我们将建立两个函数`cleanText`和`hasNoStopWords`

`cleanText`将对文本进行记号化，在空格上对其进行定界`' '`过滤掉停用词，然后`.join(' ')`将空格和`.trim()`任何可能出现在文本开头的空格组合在一起。

```
function cleanText(text) {
  return rita.RiTa.tokenize(text, '  ')
    .filter(hasNoStopWords)
    .join('  ')
    .trim()
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，标记化的文本可以输入到`hasNoStopWords`函数中进行净化，以便在`tweetData`
中使用

```
function hasNoStopWords(token) {
  const stopwords = ['@', 'http', 'RT'];
  return stopwords.every(sw => !token.includes(sw))
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经清理了数据，我们可以发推特了，所以用`inputText = inputText + ' ' + cleanText(row[5])`替换`console.log(row[5])`，然后我们可以使用`rita.RiMarkov(3)`，3 是要考虑的字数。然后使用`markov.generateSentences(1)`，1 是生成句子的数量。我们还将使用`.toString()`和`.substring(0, 140)`将结果截短为 140 个字符。

```
const tweetData =
  fs.createReadStream(filePath)
  .pipe(csvparse({
    delimiter: ','
  }))
  .on('data', function (row) {
    inputText = `${inputText}  ${cleanText(row[5])}`
  })
  .on('end', function(){
    const markov = new rita.RiMarkov(3)
    markov.loadText(inputText)
    const sentence = markov.generateSentences(1)
      .toString()
      .substring(0, 140)
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以用机器人发微博了，当有微博时，使用`.post('statuses/update'...`传入`sentence`变量作为`status`注销。

```
const tweetData =
  fs.createReadStream(filePath)
    .pipe(csvparse({
      delimiter: ','
    }))
    .on('data', row => {
      inputText = `${inputText}  ${cleanText(row[5])}`
    })
    .on('end', () => {
      const markov = new rita.RiMarkov(3)
      markov.loadText(inputText)
      const sentence = markov.generateSentences(1)
        .toString()
        .substring(0, 140)
      bot.post('statuses/update', {
        status: sentence
      }, (err, data, response) => {
        if (err) {
          console.log(err)
        } else {
          console.log('Markov status tweeted!', sentence)
        }
      })
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你想让你的句子更接近输入文本，你可以在`rita.RiMarkov(6)`中增加要考虑的单词，如果你想让它变成胡言乱语，那么就减少这个数字。

以下是完成的模块:

```
const Twit = require('twit')
const fs = require('fs')
const csvparse = require('csv-parse')
const rita = require('rita')
const config = require('./config')
const path = require('path')

let inputText = ''

const bot = new Twit(config)

const filePath = path.join(__dirname, '../twitter-archive/tweets.csv')

const tweetData =
  fs.createReadStream(filePath)
    .pipe(csvparse({
      delimiter: ','
    }))
    .on('data', row => {
      inputText = `${inputText}  ${cleanText(row[5])}`
    })
    .on('end', () => {
      const markov = new rita.RiMarkov(10)
      markov.loadText(inputText)
      const sentence = markov.generateSentences(1)
        .toString()
        .substring(0, 140)
      bot.post('statuses/update', {
        status: sentence
      }, (err, data, response) => {
        if (err) {
          console.log(err)
        } else {
          console.log('Markov status tweeted!', sentence)
        }
      })
    })
}

function hasNoStopWords(token) {
  const stopwords = ['@', 'http', 'RT']
  return stopwords.every(sw => !token.includes(sw))
}

function cleanText(text) {
  return rita.RiTa.tokenize(text, '  ')
    .filter(hasNoStopWords)
    .join('  ')
    .trim()
} 
```

Enter fullscreen mode Exit fullscreen mode

## 从 Google sheets 中检索并发布数据

如果你想发布一个链接列表，你可以使用 [`tabletop`](https://www.npmjs.com/package/tabletop) 来浏览列表，在这个例子中，我们将浏览一个链接列表。

所以，设置 bot 并要求`tabletop` :

```
const Twit = require('twit')
const config = require('./config')
const Tabletop = require('tabletop')

const bot = new Twit(config) 
```

Enter fullscreen mode Exit fullscreen mode

在您的 [`Google spreadsheet`](//sheets.google.com) 上，您需要定义一个标题，然后添加您的链接，我们将使用以下内容作为示例:

| 链接 |
| --- |
| [https://www.freecodecamp.com](https://www.freecodecamp.com) |
| [https://github.com](https://github.com) |
| [https://www.reddit.com](https://www.reddit.com) |
| [https://twitter.com](https://twitter.com) |

现在，从 Google sheets 中，我们可以选择“文件”>“发布到 web”并复制生成的链接，我们可以在桌面中使用它。

现在用三个参数初始化 Table top，`key:`是电子表格的 URL，`callback:`是获取数据的函数，`simpleSheet:`是`true`如果你只有一张表，就像我们的例子中的

```
const spreadsheetUrl = 'https://docs.google.com/spreadsheets/d/1842GC9JS9qDWHc-9leZoEn9Q_-jcPUcuDvIqd_MMPZQ/pubhtml'

Tabletop.init({
  key: spreadsheetUrl,
  callback(data, tabletop) {
    console.log(data)
  },
  simpleSheet: true
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在运行 bot 应该会给出这样的输出:

```
$ node index.js
[ { 'links': 'https://www.freecodecamp.com' },
  { 'links': 'https://github.com' },
  { 'links': 'https://www.reddit.com' },
  { 'links': 'https://twitter.com' } ] 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们可以在回调函数
中返回的`data`上使用带有`forEach`的`.post('statuses/update',...`来发送推文

```
Tabletop.init({
  key: spreadsheetUrl,
  callback(data, tabletop) {
    data.forEach(d => {
      const status = `${d.links} a link from a Google spreadsheet`;
      bot.post('statuses/update', {
        status
      }, (err, response, data) => {
        if (err) {
          console.log(err)
        } else {
          console.log('Post success!')
        }
      })
    })
  },
  simpleSheet: true
}) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，`${d.links}`是我们在 Google 电子表格中使用的标题名称，我尝试使用 skeleton 和 camel case，两者都返回了错误，所以我在电子表格中使用了单个名称标题。

这里完成的代码:

```
const Twit = require('twit')
const config = require('./config')
const Tabletop = require('tabletop')

const bot = new Twit(config)

const spreadsheetUrl = 'https://docs.google.com/spreadsheets/d/1842GC9JS9qDWHc-9leZoEn9Q_-jcPUcuDvIqd_MMPZQ/pubhtml'

Tabletop.init({
  key: spreadsheetUrl,
  callback(data, tabletop) {
    data.forEach(d => {
      const status = `${d.links} a link from a Google spreadsheet`
      console.log(status)
      bot.post('statuses/update', {
        status
      }, (err, response, data) => {
        if (err) {
          console.log(err)
        } else {
          console.log('Post success!')
        }
      })
    })
  },
  simpleSheet: true
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 把所有的东西放在一起

好的，这些例子都很好，但是我们还没有真正的解决这个问题，不是吗？我的意思是，你从终端运行它，这就完成了，我们希望能够启动机器人，让它做自己的事情。

我发现这样做的一个方法是使用`setInterval`，它将从主`bot.js`模块启动事件，所以让我们试试这个:

举个例子，我们在 tweet 上发布了一张图片，并将其添加到自己的模块中，所以从我们项目的根目录:

```
cd src
touch picture-bot.js 
```

Enter fullscreen mode Exit fullscreen mode

将示例代码粘贴到新模块中，然后我们将对`getPhoto` :
进行如下修改

```
const getPhoto = () => {
  const parameters = {
    url: 'https://api.nasa.gov/planetary/apod',
    qs: {
      api_key: process.env.NASA_KEY
    },
    encoding: 'binary'
  }
  request.get(parameters, (err, respone, body) => {
    body = JSON.parse(body)
    saveFile(body, 'nasa.jpg')
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

然后在模块底部添加:

```
module.exports = getPhoto 
```

Enter fullscreen mode Exit fullscreen mode

所以现在我们可以从我们的`bot.js`模块中的`picture-bot.js`模块调用`getPhoto`函数，我们的`bot.js`模块应该看起来像这样:

```
const picture = require('./picture-bot')

picture() 
```

Enter fullscreen mode Exit fullscreen mode

就这样，两行代码，现在试着从终端运行:

```
yarn start 
```

Enter fullscreen mode Exit fullscreen mode

我们应该得到这样的输出:

```
yarn start v0.23.4
$ node index.js
Media saved!
file PATH C:\Users\path\to\project\tweebot-play\nasa.jpg
{ media_id: 863020197799764000,
  media_id_string: '863020197799763968',
  size: 371664,
  expires_after_secs: 86400,
  image: { image_type: 'image/jpeg', w: 954, h: 944 } }
Status posted!
Done in 9.89s. 
```

Enter fullscreen mode Exit fullscreen mode

好了，今天的图片已经完成了，但是它已经运行了一次并完成了。我们需要把它放在一个带有`setInterval`的间隔上，我们需要传递两个选项给它，它要调用的函数和超时值。

图片每 24 小时更新一次，所以 24 小时内会有多少毫秒[8.64e+7]我甚至不知道ðÿ”

我是这样算出来的，1000 * 60 = 1 分钟，所以 1000 * 60 * 60 * 24 所以现在让我们把它直接加到`setInterval`函数中:

```
const picture = require('./picture-bot')

picture()
setInterval(picture, 1000 * 60 * 60 * 24) 
```

Enter fullscreen mode Exit fullscreen mode

酷，那是一个每 24 小时发布一次美国宇航局每日图片的机器人！

让我们继续，现在让我们添加一些随机性的马尔可夫机器人，就像我们在当天的图片示例中所做的那样，让我们为马尔可夫机器人创建一个新的模块，并添加上一个示例中的所有代码，所以从终端:

```
cd src
touch markov-bot.js 
```

Enter fullscreen mode Exit fullscreen mode

然后将 pasta 这个 markov bot 示例复制到新模块中，然后我们将进行以下更改:

```
const tweetData = () => {
  fs.createReadStream(filePath)
    .pipe(csvparse({
      delimiter: ','
    }))
    .on('data', row => {
      inputText = `${inputText}  ${cleanText(row[5])}`
    })
    .on('end', () => {
      const markov = new rita.RiMarkov(10)
      markov.loadText(inputText)
        .toString()
        .substring(0, 140)
      const sentence = markov.generateSentences(1)
      bot.post('statuses/update', {
        status: sentence
      }, (err, data, response) => {
        if (err) {
          console.log(err)
        } else {
          console.log('Markov status tweeted!', sentence)
        }
      })
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

然后在模块底部添加:

```
module.exports = tweetData 
```

Enter fullscreen mode Exit fullscreen mode

好的，和图片机器人的例子一样，我们将把从`markov-bot.js`导出的`tweetData`添加到我们的`bot.js`模块中，现在看起来应该是这样的:

```
const picture = require('./picture-bot')
const markov = require('./markov-bot')

picture()
setInterval(picture, 1000 * 60 * 60 * 24)

markov() 
```

Enter fullscreen mode Exit fullscreen mode

让我们让马尔可夫机器人以 5 分钟到 3 小时之间的随机间隔发推文

```
const picture = require('./picture-bot')
const markov = require('./markov-bot')

picture()
setInterval(picture, 1000 * 60 * 60 * 24)

const markovInterval = (Math.floor(Math.random() * 180) + 1) * 1000
markov()
setInterval(markov, markovInterval) 
```

Enter fullscreen mode Exit fullscreen mode

好吧。图片机器人，马尔可夫机器人，ðÿ'完成

用链接机器人做同样的事情？好的，和以前一样，你现在明白了，对吗？

在`src`文件夹中为链接机器人
创建一个新文件

```
touch link-bot.js 
```

Enter fullscreen mode Exit fullscreen mode

将链接机器人示例中的代码复制到新模块中，如下所示:

```
const link = () => {
  Tabletop.init({
    key: spreadsheetUrl,
    callback(data, tabletop) {
      data.forEach(d => {
        const status = `${d.links} a link from a Google spreadsheet`
        console.log(status)
        bot.post('statuses/update', {
          status
        }, (err, response, data) => {
          if (err) {
            console.log(err)
          } else {
            console.log('Post success!')
          }
        })
      })
    },
    simpleSheet: true
  })
}

module.exports = link 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以从 bot 中调用它，所以它看起来应该是这样的:

```
const picture = require('./picture-bot')
const markov = require('./markov-bot')
const link = require('./link-bot')

picture()
setInterval(picture, 1000 * 60 * 60 * 24)

const markovInterval = (Math.floor(Math.random() * 180) + 1) * 1000
markov()
setInterval(markov, markovInterval)

link()
setInterval(link, 1000 * 60 * 60 * 24) 
```

Enter fullscreen mode Exit fullscreen mode

好吗？很酷吧

我们现在可以让机器人运行来做它的事情了！！

## 部署到`now`

是的，我们有一个机器人做一些事情，但它在我们的开发环境中，所以它不能永远留在那里，它可以，但它会非常不典型。让我们把我们的机器人放在某个服务器上做它的事情。

为此，我们将使用 [`now`](https://zeit.co/now) ，`now`允许从 CLI 进行简单部署，如果您现在还不熟悉，请快速浏览一下[文档](https://zeit.co/now)，在这些示例中，我们将使用`now-cli`。

为了让我们的机器人在 [`now`](https://zeit.co/now) 上做好准备，我们需要做一些事情，让我们快速列出它们，然后再详细说明。

*   注册并安装`now-cli`
*   添加`now`设置+ `.npmignore`文件
*   添加`.env`变量作为秘密
*   添加 npm `deploy`脚本
*   环形夹具`picture-bot.js`

准备好了吗？让我们开始吧！ðŸ'

**注册并安装`now-cli`**

首先让我们注册[zeit](https://zeit.co/login)——创建一个帐户并认证，然后我们可以安装 CLI。

在我们的机器上全局安装`now`，这样你就可以在任何地方使用它，从终端输入
来安装`now-cli`

```
npm install -g now 
```

Enter fullscreen mode Exit fullscreen mode

完成后，使用
登录

```
now --login 
```

Enter fullscreen mode Exit fullscreen mode

第一次运行`now`时，它会询问你的电子邮件地址，以便识别你的身份。进入登录时提供的电子邮件帐户，点击`now`发送给你的邮件，你将自动登录。

如果您需要切换帐户或重新进行身份验证，请再次运行相同的命令。

您可以随时查看 [`now-cli`](https://zeit.co/docs/getting-started/installing-now#cli-with-npm) 文档，了解更多信息以及 [`your first deployment`](https://zeit.co/docs/getting-started/your-first-deployments#deploying-node) 指南。

**添加`now`设置**

好了，注册和安装已经完成，我们现在可以配置 bot 来部署到`now`。首先让我们把`now`设置添加到我们的`package.json`文件中，我已经把它放在我的`npm`脚本和我的`package.json` :
中的作者名字之间

```
"scripts":  {  "start":  "node index.js"  },  "now":  {  "alias":  "my-awesome-alias",  "files":  [  "src",  "index.js"  ]  },  "author":  "Scott Spence", 
```

Enter fullscreen mode Exit fullscreen mode

这对我来说是一个很大的困惑，所以我希望我可以帮你省掉我试图配置它的痛苦，所有相关的文档都在那里，你只需要把它们放在一起，ðÿ˜ž

> 如果您在这里发现任何没有意义或完全错误的内容，请[记录问题](https://github.com/spences10/twitter-bot-playground/issues/new)或创建拉取请求ðÿ'

现在的设置`alias`是给你的部署一个由`now`创建的自动生成的 URL 的昵称，`files`部分包含了我们想要在对`now`的部署中包含的内容，我们将很快检查文件结构。基本上，`files`阵列中包含的是所有被推送到 now 服务器的内容。

一切都好吗？

好了，现在我们需要在项目的根目录下添加一个`.npmignore`文件，并在其中添加下面一行:

```
!tweets.csv 
```

Enter fullscreen mode Exit fullscreen mode

`tweets.csv`需要上传到`now`服务器才能被机器人使用，但是我们之前已经把它包含在了我们的`.gitignore`中，当你的项目被加载到服务器时`now`用它来构建你的项目。这意味着文件不会被加载，除非我们添加了`.npmignore`来避免忽略`tweets.csv`ðÿ˜…

**添加`.env`变量作为秘密**

好的，我们的超级秘密 Twitter 密匙将需要作为`secrets`存储在`now`中。这是一个非常好的功能，你可以定义任何东西为秘密，并引用它作为`now`的别名。

让我们开始，所以语法是`now secrets add my-secret "my value"`所以对于我们的`.env`键，把它们都加起来，给它们一个描述性的【但是要简短！]姓名。

> 你不需要把你的“我的价值”用引号括起来，但是文档上说“如果有疑问，把你的价值用引号括起来”

好，那么从终端`now secrets ls`应该列出你刚刚创建的`secrets`:

```
$ now secrets ls
> 5 secrets found under spences10 [1s]

                            id name                   created
  sec_xxxxxxxxxxZpLDxxxxxxxxxx  ds-twit-key            23h ago
  sec_xxxxxxxxxxTE5Kxxxxxxxxxx  ds-twit-secret         23h ago
  sec_xxxxxxxxxxNorlxxxxxxxxxx  ds-twit-access         23h ago
  sec_xxxxxxxxxxMe1Cxxxxxxxxxx  ds-twit-access-secret  23h ago
  sec_xxxxxxxxxxMJ2jxxxxxxxxxx  nasa-key               23h ago 
```

Enter fullscreen mode Exit fullscreen mode

**添加 npm `deploy`脚本**

现在我们已经定义了秘密，我们可以创建一个部署脚本来部署到`now`，所以在我们的`package.json`中，让我们添加一个额外的脚本:

```
 "main":  "index.js",  "scripts":  {  "start":  "node index.js",  "deploy":  "now -e CONSUMER_KEY=@ds-twit-key -e CONSUMER_SECRET=@ds-twit-secret -e ACCESS_TOKEN=@ds-twit-access  -e ACCESS_TOKEN_SECRET=@ds-twit-access-secret -e NASA_KEY=@nasa-key"  },  "now":  { 
```

Enter fullscreen mode Exit fullscreen mode

让我们回顾一下我们在那里添加的内容，`deploy`将运行`now`命令，并将我们所有的环境`-e`变量和相关的`secret`值传递给它，如果我们将其分解成单独的行，会更清楚一些:

```
now 
-e CONSUMER_KEY=@ds-twit-key 
-e CONSUMER_SECRET=@ds-twit-secret 
-e ACCESS_TOKEN=@ds-twit-access  
-e ACCESS_TOKEN_SECRET=@ds-twit-access-secret 
-e NASA_KEY=@nasa-key 
```

Enter fullscreen mode Exit fullscreen mode

**Re 夹具`picture-bot.js`T2】**

好的，因为`now`部署是[不可变的](https://blog.codeship.com/immutable-deployments/)这意味着对我们想要保存 NASA 当天照片的磁盘没有写访问权，所以为了避开这个问题，我们需要使用`/tmp`文件位置。

向来自`zeit`的[@蒂姆](https://github.com/timneutkens)大声欢呼，感谢他帮我解决了这个问题！

在`picture-bot.js`模块中，将下面两行添加到模块顶部:

```
const os = require('os')
const tmpDir = os.tmpdir() 
```

Enter fullscreen mode Exit fullscreen mode

这两行给了我们操作系统的`temp`目录，所以如果你和我一样在 Windows 上，它会和你在另一个系统上一样好，比如基于 linux 的系统，这就是`now`。在我们的`saveFile`函数中，我们将使用`tmpDir`来保存我们的文件。

我们已经从`getPhoto`函数中取出了`nasa.jpg`，因为我们可以在`saveFile`函数中定义该信息，NASA 的 potd 不仅仅是一个`'jpeg`,那里发布的一些项目也是视频。我们可以用一个来自被传入的`body`的[三元函数](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)来定义类型，这将发送一条带有视频链接的推文:

```
function saveFile(body) {
  const fileName = body.media_type === 'image/jpeg' ? 'nasa.jpg' : 'nasa.mp4';
  const filePath = path.join(tmpDir + `/${fileName}`)

  console.log(`saveFile: file PATH ${filePath}`)
  if (fileName === 'nasa.mp4') {
    // tweet the link
    const params = {
      status: 'NASA video link: ' + body.url
    }
    postStatus(params)
    return
  }
  const file = fs.createWriteStream(filePath)

  request(body).pipe(file).on('close', err => {
    if (err) {
      console.log(err)
    } else {
      console.log('Media saved!')
      const descriptionText = body.title
      uploadMedia(descriptionText, filePath)
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

这里完成的代码:

```
const Twit = require('twit')
const request = require('request')
const fs = require('fs')
const config = require('./config')
const path = require('path')

const bot = new Twit(config)

const os = require('os')
const tmpDir = os.tmpdir()

const getPhoto = () => {
  const parameters = {
    url: 'https://api.nasa.gov/planetary/apod',
    qs: {
      api_key: process.env.NASA_KEY
    },
    encoding: 'binary'
  }
  request.get(parameters, (err, respone, body) => {
    body = JSON.parse(body)
    saveFile(body)
  })
}

function saveFile(body) {
  const fileName = body.media_type === 'image/jpeg' ? 'nasa.jpg' : 'nasa.mp4';
  const filePath = path.join(tmpDir + `/${fileName}`)

  console.log(`saveFile: file PATH ${filePath}`)
  if (fileName === 'nasa.mp4') {
    // tweet the link
    const params = {
      status: 'NASA video link: ' + body.url
    }
    postStatus(params)
    return
  }
  const file = fs.createWriteStream(filePath)

  request(body).pipe(file).on('close', err => {
    if (err) {
      console.log(err)
    } else {
      console.log('Media saved!')
      const descriptionText = body.title
      uploadMedia(descriptionText, filePath)
    }
  })
}

function uploadMedia(descriptionText, fileName) {
  console.log(`uploadMedia: file PATH ${fileName}`)
  bot.postMediaChunked({
    file_path: fileName
  }, (err, data, respone) => {
    if (err) {
      console.log(err)
    } else {
      console.log(data)
      const params = {
        status: descriptionText,
        media_ids: data.media_id_string
      }
      postStatus(params)
    }
  })
}

function postStatus(params) {
  bot.post('statuses/update', params, (err, data, respone) => {
    if (err) {
      console.log(err)
    } else {
      console.log('Status posted!')
    }
  })
}

module.exports = getPhoto 
```

Enter fullscreen mode Exit fullscreen mode

好，就这样！我们准备部署到`now`！ðŸš€

因此，从终端我们调用我们之前定义的部署脚本:

```
yarn deploy 
```

Enter fullscreen mode Exit fullscreen mode

您将得到一些输出:

```
Î» yarn deploy
yarn deploy v0.24.4
$ now -e CONSUMER_KEY=@ds-twit-key -e CONSUMER_SECRET=@ds-twit-secret -e ACCESS_TOKEN=@ds-twit-access  -e ACCESS_TOKEN_SECRET=@ds-twit-access-secret -e NASA_KEY=@nasa-key
> Deploying ~\gitrepos\tweebot-play under spences10
> Using Node.js 7.10.0 (default)
> Ready! https://twee-bot-play-rapjuiuddx.now.sh (copied to clipboard) [5s]
> Upload [====================] 100% 0.0s
> Sync complete (1.54kB) [2s]
> Initializing…
> Building
> â–² npm install
> â§— Installing:
>  â€£ csv-parse@^1.2.0
>  â€£ dotenv@^4.0.0
>  â€£ rita@^1.1.63
>  â€£ tabletop@^1.5.2
>  â€£ twit@^2.2.5
> âœ“ Installed 106 modules [3s]
> â–² npm start
> > tweet-bot-playground@1.0.0 start /home/nowuser/src
> > node index.js
> saveFile: file PATH /tmp/nasa.jpg
> Media saved!
> uploadMedia: file PATH /tmp/nasa.jpg 
```

Enter fullscreen mode Exit fullscreen mode

呜！你已经部署了你的机器人！ðŸ™Œ

如果你点击产生的链接，你将能够检查机器人，因为它是在`now`页面上还有一个方便的日志部分，你可以检查输出。ðŸ'

## 资源

令人敬畏的推特机器人

如果你想了解我更多，你可以[问我任何事情](https://github.com/spences10/ama)查看[我的 Github](https://github.com/spences10) ，或者发微博给我 [@ScottDevTweets](https://twitter.com/ScottDevTweets) 。