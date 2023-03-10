# 构建 WordStream

> 原文：<https://dev.to/marcroberts/building-wordstream-1jl5>

上周，我花了几个小时玩一些新技术，构建了一个从 twitter [样本流](https://dev.twitter.com/streaming/overview)生成的实时词云 [wordstream](http://twitter-word-stream.herokuapp.com/) 。以下是方法。

twitter 流媒体 API 是将你感兴趣的推文推送给你的一种非常有效的方式。例如，你可以使用[过滤器](https://dev.twitter.com/streaming/reference/post/statuses/filter)端点来匹配你的过滤器(作者、标签、关键词等)的推文，但对此我更感兴趣的是[样本](https://dev.twitter.com/streaming/reference/get/statuses/sample)端点，它发出了大约 1%的公共推文。然而，这个端点有一些限制:

*   一组凭据(应用程序/用户组合)只能打开一个连接(任何进一步的连接尝试都将终止之前的连接)。因此，为了使用它，我要么需要让每个访问者通过应用程序验证，以便创建他们自己的流连接，要么建立某种服务器端代理。
*   API 响应实际上非常大，当与每秒收到的数百条推文结合在一起时，会检索到大量数据(在周五早上的测试中，我从 API 获得了相当稳定的 2 Mbps 数据)。

这里有一个快速的例子(捕获大约 5 秒钟的流，产生 1.3 MB 的数据，我在这里只展示了前几条推文，你可以说是样本的一个例子)关于流式 API 数据:

```
{ 
    created_at: 'Mon Jan 26 16:21:26 +0000 2015',
    id: 559747954651971600,
    id_str: '559747954651971584',
    text: 'Мосгорсуд оставил под арестом до 16 апреля Александра Кольченко, фигуранта дела ...',
    source: '<a href="http://ifttt.com" rel="nofollow">IFTTT</a>',
    truncated: false,
    in_reply_to_status_id: null,
    in_reply_to_status_id_str: null,
    in_reply_to_user_id: null,
    in_reply_to_user_id_str: null,
    in_reply_to_screen_name: null,
    user:
     { id: 2687442584,
       id_str: '2687442584',
       name: 'Галина Никандровa',
       screen_name: 'Byce6A',
       location: '',
       url: null,
       description: null,
       protected: false,
       verified: false,
       followers_count: 210,
       friends_count: 121,
       listed_count: 1,
       favourites_count: 0,
       statuses_count: 73725,
       created_at: 'Mon Jul 28 12:45:30 +0000 2014',
       utc_offset: null,
       time_zone: null,
       geo_enabled: false,
       lang: 'ru',
       contributors_enabled: false,
       is_translator: false,
       profile_background_color: 'C0DEED',
       profile_background_image_url: 'http://abs.twimg.cimg/themes/theme1/bg.png',
       profile_background_image_url_https: 'https://abs.twimg.cimg/themes/theme1/bg.png',
       profile_background_tile: false,
       profile_link_color: '0084B4',
       profile_sidebar_border_color: 'C0DEED',
       profile_sidebar_fill_color: 'DDEEF6',
       profile_text_color: '333333',
       profile_use_background_image: true,
       profile_image_url: 'http://abs.twimg.com/sticky/default_profile_images/default_profile_1_normal.png',
       profile_image_url_https: 'https://abs.twimg.com/sticky/default_profile_images/default_profile_1_normal.png',
       default_profile: true,
       default_profile_image: true,
       following: null,
       follow_request_sent: null,
       notifications: null },
    geo: null,
    coordinates: null,
    place: null,
    contributors: null,
    retweet_count: 0,
    favorite_count: 0,
    entities:
     { hashtags: [],
       trends: [],
       urls: [],
       user_mentions: [],
       symbols: [] },
    favorited: false,
    retweeted: false,
    possibly_sensitive: false,
    filter_level: 'low',
    lang: 'ru',
    timestamp_ms: '1422289286660'
},
{
    created_at: 'Mon Jan 26 16:21:26 +0000 2015',
    id: 559747954639384600,
    id_str: '559747954639384577',
    text: 'Beautiful life is so much better than Carry you tbh',
    source: '<a href="http://twitter.com" rel="nofollow">Twitter Web Client</a>',
    truncated: false,
    in_reply_to_status_id: null,
    in_reply_to_status_id_str: null,
    in_reply_to_user_id: null,
    in_reply_to_user_id_str: null,
    in_reply_to_screen_name: null,
    user:
     { id: 2974152997,
       id_str: '2974152997',
       name: 'Sandra Young',
       screen_name: 'edwardalazobuy1',
       location: 'West Virginia',
       url: 'http://optimizedirectory.com/',
       description: '1D / Glee / T-Swizzle / Narnia / Criminal Minds / KSS 8 / Lucky #18/ #23 / #24 / Directioner / MATTHEW GRAY GUBLER FOR DA WIN! / Louis\' pants',
       protected: false,
       verified: false,
       followers_count: 0,
       friends_count: 1,
       listed_count: 0,
       favourites_count: 0,
       statuses_count: 37,
       created_at: 'Sun Jan 11 06:10:53 +0000 2015',
       utc_offset: null,
       time_zone: null,
       geo_enabled: false,
       lang: 'en',
       contributors_enabled: false,
       is_translator: false,
       profile_background_color: 'C0DEED',
       profile_background_image_url: 'http://abs.twimg.cimg/themes/theme1/bg.png',
       profile_background_image_url_https: 'https://abs.twimg.cimg/themes/theme1/bg.png',
       profile_background_tile: false,
       profile_link_color: '0084B4',
       profile_sidebar_border_color: 'C0DEED',
       profile_sidebar_fill_color: 'DDEEF6',
       profile_text_color: '333333',
       profile_use_background_image: true,
       profile_image_url: 'http://pbs.twimg.com/profile_images/559450280236830720/fGI9TXLt_normal.png',
       profile_image_url_https: 'https://pbs.twimg.com/profile_images/559450280236830720/fGI9TXLt_normal.png',
       profile_banner_url: 'https://pbs.twimg.com/profile_banners/2974152997/1422261339',
       default_profile: true,
       default_profile_image: false,
       following: null,
       follow_request_sent: null,
       notifications: null },
    geo: null,
    coordinates: null,
    place: null,
    contributors: null,
    retweet_count: 0,
    favorite_count: 0,
    entities:
     { hashtags: [],
       trends: [],
       urls: [],
       user_mentions: [],
       symbols: [] },
    favorited: false,
    retweeted: false,
    possibly_sensitive: false,
    filter_level: 'low',
    lang: 'en',
    timestamp_ms: '1422289286657'
},
{ 
    created_at: 'Mon Jan 26 16:21:26 +0000 2015',
    id: 559747954672943100,
    id_str: '559747954672943104',
    text: 'Saints win 2-0! Enppi are 0-0 so double chance looking good on this one too.',
    source: '<a href="http://twitter.com/download/iphone" rel="nofollow">Twitter for iPhone</a>',
    truncated: false,
    in_reply_to_status_id: null,
    in_reply_to_status_id_str: null,
    in_reply_to_user_id: null,
    in_reply_to_user_id_str: null,
    in_reply_to_screen_name: null,
    user:
     { id: 2960224947,
       id_str: '2960224947',
       name: 'The Secret Tipster',
       screen_name: 'Secret_Tipster_',
       location: '',
       url: null,
       description: 'FREE betting tips and £10-£1,000 challenges! \n\n5pts - Strong tip (high stakes)\n3pts - Good tip (medium stakes)\n1pt - Fair tip (low stakes)',
       protected: false,
       verified: false,
       followers_count: 343,
       friends_count: 1588,
       listed_count: 2,
       favourites_count: 104,
       statuses_count: 290,
       created_at: 'Sun Jan 04 14:09:31 +0000 2015',
       utc_offset: 0,
       time_zone: 'London',
       geo_enabled: false,
       lang: 'en-gb',
       contributors_enabled: false,
       is_translator: false,
       profile_background_color: '000000',
       profile_background_image_url: 'http://abs.twimg.cimg/themes/theme1/bg.png',
       profile_background_image_url_https: 'https://abs.twimg.cimg/themes/theme1/bg.png',
       profile_background_tile: false,
       profile_link_color: '89C9FA',
       profile_sidebar_border_color: '000000',
       profile_sidebar_fill_color: '000000',
       profile_text_color: '000000',
       profile_use_background_image: false,
       profile_image_url: 'http://pbs.twimg.com/profile_images/551742687452229634/Q2rfimMq_normal.png',
       profile_image_url_https: 'https://pbs.twimg.com/profile_images/551742687452229634/Q2rfimMq_normal.png',
       default_profile: false,
       default_profile_image: false,
       following: null,
       follow_request_sent: null,
       notifications: null },
    geo: null,
    coordinates: null,
    place: null,
    contributors: null,
    retweet_count: 0,
    favorite_count: 0,
    entities:
     { hashtags: [],
       trends: [],
       urls: [],
       user_mentions: [],
       symbols: [] },
    favorited: false,
    retweeted: false,
    possibly_sensitive: false,
    filter_level: 'low',
    lang: 'en',
    timestamp_ms: '1422289286665' 
} 
```

以下是一些需要注意的事项:

*   有很多我不需要的关于 tweets 的元数据。
*   有相当多的本地转发，包括在新推文中以 RT 为前缀的转发文本。应该把它们排除在外还是应该把转发计入字数？
*   有许多不同的语言，为了让自己有一些有意义的东西(我只能流利地说英语，再加上一些其他欧洲语言，充其量很差)，我决定只处理英语推文。

所有这些意味着构建一个简单的后端服务/代理是有意义的，它创建一个单一的流连接，处理这些数据，并向浏览器提供更精简的数据量。我选择用 [node.js](http://nodejs.org/) 构建一些东西。

首先，我们需要从流式 API 中获取数据。我发现了一个名为 [node-tweet-stream](https://www.npmjs.com/package/node-tweet-stream) 的 npm 模块，它与过滤器端点一起工作，稍加修改就可以将其连接到示例 API。

```
var twitter = require('./twitter-stream'), //
    stream;

stream = new twitter({
  consumer_key: 'xxx',
  consumer_secret: 'xxx',
  token: 'xxx',
  token_secret: 'xxx'
});

stream.on('tweet', function(tweet) {
  console.log(tweet);
});

stream.connect(); 
```

我经常使用 [Heroku](https://heroku.com) 来托管像这样的小东西，Heroku 鼓励你在环境中存储尽可能多的应用程序配置，而不是你的应用程序代码库。为了在我的 Ruby 项目中管理这一点，我使用 [dotenv](https://github.com/bkeepers/dotenv) 来允许我将这样的配置保存在本地的一个`.env`文件中(从源代码控制中排除它)。我很高兴地发现在 node 中也存在这样的开发功能。一个快速安装的 [dotenv](https://www.npmjs.com/package/dotenv) npm 模块和一个简单的`require`在这里工作。

将事情记录到控制台对于调试来说是很棒的，但是没有真正的用途。为了将数据输出到浏览器，我开始构建一个简单的 [express](http://expressjs.com/) 应用程序，因为我以前在这方面有过一些经验，但有些东西让我想起了 web sockets 和 [socket.io](http://socket.io/) ，所以我想我应该尝试使用它们。同样，所需要的只是另一个 install/require 和几行额外的代码，现在我们已经将 tweets 代理到浏览器了。代码现在看起来像这样:

```
var app = require('express')(),
    dotenv = require('dotenv'),
    server = require('http').Server(app),
    io = require('socket.io')(server),
    twitter = require('./twitter-stream'),
    stream;

dotenv.load();

stream = new twitter({
  consumer_key: process.env.TWITTER_CONSUMER_KEY,
  consumer_secret: process.env.TWITTER_CONSUMER_SECRET,
  token: process.env.TWITTER_TOKEN,
  token_secret: process.env.TWITTER_TOKEN_SECRET
});

server.listen(process.env.PORT || 5000);

stream.on('tweet', function(tweet) {
  io.emit('tweet', tweet);
});

stream.connect(); 
```

代理数据的主要原因是为了减少发送到浏览器的数据量，所以现在是时候将这些大量的响应减少到一些单词列表中了。我再次发现了一些很棒的 npm 模块来帮助解决这个问题；[关键词提取器](https://www.npmjs.com/package/keyword-extractor)用于提取重要的词(或者更准确地说，排除不重要的词)，以及[法郎](https://www.npmjs.com/package/franc)用于确定推文的语言(关键词提取器只对英语有效，很像我的大脑)。

> 在写这篇文章的时候，我注意到 twitter 响应实际上包含了一个`lang`字段，否定了使用 franc 的必要性。我当时没有注意到这一点，哦，好吧！

插入这些内容，加上一些我自己排除的内容(链接、转发、回复)，我们得到了部署到 Heroku:
的最终代码(在 [GitHub](https://github.com/marcroberts/wordstream) 上找到)

```
var app = require('express')(),
    dotenv = require('dotenv'),
    server = require('http').Server(app),
    io = require('socket.io')(server),
    xt = require('keyword-extractor'),
    franc = require('franc'),
    twitter = require('./twitter-stream'),
    stream;

dotenv.load();

stream = new twitter({
  consumer_key: process.env.TWITTER_CONSUMER_KEY,
  consumer_secret: process.env.TWITTER_CONSUMER_SECRET,
  token: process.env.TWITTER_TOKEN,
  token_secret: process.env.TWITTER_TOKEN_SECRET
});

io.set('origins', '*:*');

server.listen(process.env.PORT || 5000);

function exceptions(word){
  if (word.match(/https?:/)) return false; // links
  if (word.match(/^@/)) return false; // replies
  if (word.match(/&|\/|"/)) return false; // random punctuation

  return true;
}

stream.on('tweet', function(tweet) {

  // ignore retwets
  if (tweet.retweeted_status || tweet.text.match(/^RT/)) return;

  // only english for now
  if (franc(tweet.text) != 'eng') return;

  // parse that tweet, extract words
  words = xt.extract(tweet.text,{
    language:"english",
    remove_digits: true,
    return_changed_case:true
  }).filter(exceptions);

  if (words.length > 0) io.emit('tweet', words);
});

stream.connect(); 
```

因此，用不到 50 行代码，我们就可以对实时 tweets 进行单词解析，并将单词列表发送到浏览器。现在让浏览器来呈现它们。

这将几乎完全是 javascript 驱动的，所以我将专注于此，如果你对 HTML 和 CSS 感兴趣，那么看看源代码，问我你可能有的任何问题。

首先，我们将使用 socket.io 连接到 web 套接字，并在单词出现时开始抓取它们。

> 我在这里使用了[下划线. js](http://underscorejs.org/) 库来访问一些简单的帮助函数

```
var socket = io.connect('wss://twitter-word-stream.herokuapp.com/');

socket.on('tweet', function (data) {
  _.each(data, function(word) {
    console.log(word);
  });
}); 
```

就这样，文字被吐到浏览器的控制台上，但当然这没有实际用途。让我们计算出现的次数并直观地显示出来。我们将通过把单词和它们的计数放入一个对象中，然后周期性地显示最流行的单词来做到这一点。

```
var socket = io.connect('wss://twitter-word-stream.herokuapp.com/'),
    word_counts = {},
    text_nodes = {},
    frame = 0;

function render() {
  var max = 0,
      displayed_words = [];

  // increment frame counter
  frame++;

  _.each(word_counts, function(count) {
    if (count > max) max = count;
  });

  // filter them to just the most popular ones
  displayed_words = _.sortBy(_.keys(word_counts), function(word) {
    return max - word_counts[word];
  }).slice(0,30);

  _.each(displayed_words, function(word) {
    var size = words[word] / max,
        text, node;

    // create the text node if need be
    if (!text_nodes[word]) {
      text = document.createTextNode(word);
      node = document.createElement('span');

      // position kind of in the middle somewhere
      var top = 80*Math.random();
      var left = 70*Math.random();

      // give it a random pastelly colour
      node.setAttribute('style', "top: " + top + "%; left: " + left + '%; color: hsla('+360*Math.random()+',50%,50%,0.75)');

      node.appendChild(text);
      document.body.appendChild(node);
      text_nodes[word] = {
        updated: frame,
        node: node
      };
    } else {
      text_nodes[word].updated = frame;
    }

    // clear expired words
    _.each(text_nodes, function(obj, word) {
      if (obj.updated < frame) {
        obj.node.remove();
        delete text_nodes[word];
      }
    });

    // size it relative to it's occurence
    text_nodes[word].node.style.transform = 'scale(' + (0.2 + size*0.8) + ')';
    text_nodes[word].node.style.webkitTransform = 'scale(' + (0.2 + size*0.8) + ')';

  });

}

setInterval(render, 500);

socket.on('tweet', function (data) {
  _.each(data, function(word) {
    word_counts[word] = (word_counts[word] || 0) + 1;
  });
}); 
```

这里有几件事需要解释:

*   使用了`scale`转换而不是`font-size`来改变单词的大小，因为这导致了 GPU 加速转换，然后我们可以在对性能影响很小的情况下增强转换。
*   创建的 DOM 节点被缓存在`text_nodes`对象中，所以我们不必每次都重新创建它们或者试图找到它们。
*   框架编号用于记录元素上次更新的时间，这样就可以很容易地删除任何不再流行的单词。
*   使用 [`hsla()`](http://css-tricks.com/yay-for-hsla/) 将单词的颜色随机化，因为这仅需要生成一个数字(色调)，而不是使用`rgba()`所需的多个数字。

这个功能很棒，但是它会计算出现次数，因为你第一次加载页面时，我想让它只考虑最近的单词(假设只是最近 5 分钟)，所以我需要以这样一种方式存储单词列表，以便我可以轻松快速地删除旧的单词。我可以存储每个单词每次出现的时间，但那会变得复杂。相反，我决定将单词 occurences 存储在几个不同的对象中(我称之为 buckets ),每隔几秒钟轮换一次。然后,`render`方法将只使用覆盖最后 5 分钟事件的桶。

```
var socket = io.connect('wss://twitter-word-stream.herokuapp.com/'),

    text_nodes = {},
    frame = 0,

    current_bucket = {},
    buckets = [current_bucket],

    bucket_count = 30, // how many buckets to remember
    bucket_width = 10; // how many seconds worth of words to keep in the buckets

function render() {
  var max = 0,
      words = {},
      displayed_words = [];

  // increment frame counter
  frame++;

  // get counts of words across all buckets
  _.each(buckets, function(bucket){
    _.each(bucket, function(count, word) {
      words[word] = (words[word] || 0) + count;
      if (count > max) max = count;
    });
  });

  // filter them to just the most popular ones

  displayed_words = _.sortBy(_.keys(words), function(word) {
    return max - words[word];
  }).slice(0,30);

  _.each(displayed_words, function(word) {
    var size = words[word] / max,
        text, node;

    // create the text node if need be
    if (!text_nodes[word]) {
      text = document.createTextNode(word);
      node = document.createElement('span');

      // position kind of in the middle somewhere
      var top = 80*Math.random();
      var left = 70*Math.random();

      // give it a random pastelly colour
      node.setAttribute('style', "top: " + top + "%; left: " + left + '%; color: hsla('+360*Math.random()+',50%,50%,0.75)');

      node.appendChild(text);
      document.body.appendChild(node);
      text_nodes[word] = {
        updated: frame,
        node: node
      };
    } else {
      text_nodes[word].updated = frame;
    }

    // clear expired words
    _.each(text_nodes, function(obj, word) {
      if (obj.updated < frame) {
        obj.node.remove();
        delete text_nodes[word];
      }
    });

    // size it relative to it's occurence
    text_nodes[word].node.style.transform = 'scale(' + (0.2 + size*0.8) + ')';
    text_nodes[word].node.style.webkitTransform = 'scale(' + (0.2 + size*0.8) + ')';

  });

}

function rotate_buckets() {

  current_bucket = {};
  buckets.push(current_bucket);

  while (buckets.length >= bucket_count) buckets.shift();

}

setInterval(rotate_buckets, bucket_width*1000);
setInterval(render, 500);

socket.on('tweet', function (data) {
  _.each(data, function(word) {
    current_bucket[word] = (current_bucket[word] || 0) + 1;
  });
}); 
```

那里我们有(或多或少)完成的代码，这里它运行在 [Heroku](https://twitter-word-stream.herokuapp.com/) 上

当我有能力的时候，我仍然想改进一些事情:

*   文字的位置是随机的，这通常会导致过度重叠，半透明有助于解决这个问题，但有时会很糟糕。
*   如果它能更个性化一点就好了，也许源是一个标签，一个用户或者你的时间线，而不是样本流。

花几个小时玩一些新东西是很有趣的，每个人都需要能够偶尔这样做。

你最感兴趣的新技术是什么？

* * *

更新:这一切的来源可以在 GitHub 上找到

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [马克罗伯茨](https://github.com/marcroberts) / [ wordstream](https://github.com/marcroberts/wordstream)

### 一个提取单词的 twitter 流媒体客户端

<article class="markdown-body entry-content container-lg" itemprop="text">

# node-js-入门

一个使用 [Express 4](http://expressjs.com/) 的准系统 Node.js 应用。

这个应用程序支持 Heroku 文章上的【Node 入门——来看看吧。

## 本地运行

确保你已经安装了 [Node.js](http://nodejs.org/) 和 [Heroku Toolbelt](https://toolbelt.heroku.com/) 。

```
$ git clone git@github.com:heroku/node-js-getting-started.git # or clone your own fork
$ cd node-js-getting-started
$ npm install
$ npm start
```

您的应用程序现在应该运行在 [localhost:5000](http://localhost:5000/) 上。

## 部署到 Heroku

```
$ heroku create
$ git push heroku master
$ heroku open 
```

## 证明文件

有关在 Heroku 上使用 Node.js 的更多信息，请参阅以下开发中心文章:

*   [Heroku 上的 Node.js 入门](https://devcenter.heroku.com/articles/getting-started-with-nodejs)
*   [Heroku Node.js 支持](https://devcenter.heroku.com/articles/nodejs-support)
*   [Heroku 上的 node . js](https://devcenter.heroku.com/categories/nodejs)
*   [node . js 开发的最佳实践](https://devcenter.heroku.com/articles/node-best-practices)
*   [通过 Node.js 在 Heroku 上使用 web sockets](https://devcenter.heroku.com/articles/node-websockets)

</article>

[View on GitHub](https://github.com/marcroberts/wordstream)