# 如何重构一个动漫简讯

> 原文：<https://dev.to/maxwell_dev/how-to-refactor-an-anime-newsletter-2f63>

作为一名初级开发人员，我已经接受了阅读旧代码的奇异感觉。这是对我过去有多坏的厌恶和对我过去有多坏的喜悦的混合。这种情况在 JavaScript 中最常见，因为我花了很多时间来编写和改进它。

当我重新专注于学习 JavaScript 时，我重新访问了我最喜欢的节点程序之一。我花了一个下午的时间来写这篇文章，但这几个月来每天都让我开心。。六个月前，我在这里写了一篇文章，讲述了它如何从 Twitter 账户上抓取最受欢迎的壁纸，并在每天早上通过电子邮件发送给我。

半年后，我在写我最近是如何重构它的。

### 最大的变化:增加了更多的 Twitter 账户

我的程序的主要部分使用 Twitter 的 API 来访问一个 Twitter 帐户的推文。它将导出另一个 JavaScript 文件可以解析的 Promise 对象——这是可行的，但不是非常模块化。理想情况下，我希望导出一个已经被裁剪和排序的 JSON 对象。

更雄心勃勃的是，我想从更多的 Twitter 账户中获取数据！

#### 发出所有的推特请求

我不确定从哪里开始，所以我很简单:在一个数组中列出所有的 Twitter 账户。

```
const TWITTER_ACCOUNTS = ['AceWallpaperBot', 'AceYuriBot', 'anipictures']; 
```

Enter fullscreen mode Exit fullscreen mode

现在我不得不将它转化成几个 Twitter API 请求。我调整了旧代码，做了一个简单的函数，可以为任何帐户发出请求，同时也使用了 ES6 字符串插值。

```
function twitter_request(username) {
  return T.get('search/tweets', { q: `from:${username} since:` + date.yesterday + ' until:' + date.today, count: 100 });
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我有了一个帐户名数组和一个函数，该函数可以接受这些名称并返回带有结果的 Promise 对象。六个月前，我会做一个 for 循环，将每个承诺推送到另一个数组。今天我知道了一个更有效的选择:使用地图和箭头功能！这会将每个帐户作为一行中新数组的一部分传递给函数。

```
const twitter_api_requests = TWITTER_ACCOUNTS.map((account) => twitter_request(account)); 
```

Enter fullscreen mode Exit fullscreen mode

我所有的承诺都等着兑现呢！

#### 并行解决三个承诺

这很好，但是留下了一个问题:我有三个承诺，解决它们的最好方法是什么？

我的第一个想法是“一次一个”，但这很浪费。承诺应该异步操作——我应该尝试一下子解决它们。

有了`Promise.all`才有可能。它接受一个承诺数组，当我传递的每个承诺都解析时，它也解析。我很方便地拥有一个 promise 对象数组，所以它非常完美！

六个月前我会把它放在一个常规函数里。但是今天的我(google 搜索“异步 JavaScript”)有一个更好的想法:**异步函数。**它们就像常规函数，但返回其他承诺对象，并使用`await`表达式暂停函数，直到其他承诺解决。

你可能会问自己，为什么我要用这个？这是我异步思考的一部分。Twitter API 请求需要时间来返回数据，因此依赖它的函数必须等待。否则，函数可能会在数据存在之前运行。异步函数知道等待，在这里非常完美。这听起来很基本，但是我已经习惯了 JavaScript 的直线发展，这对我来说是一个很大的转变。

```
async function get_twitter_api_results() {
  try {
    let twitter_api_results = await Promise.all(twitter_api_requests);

    return twitter_api_requests;
  }

  catch(error) { console.log(error); }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数将在第三行暂停，直到获得所有 Twitter 数据。一旦完成，函数就恢复并返回数据——它不会太快完成并返回`undefined`。我还使用了一个`try...catch`块，以便在承诺失败时更容易管理错误。

该函数在每个请求解决后返回我的 Twitter 数据。现在是组织这一切的有趣部分！

#### 抢占前五的壁纸

这部分类似于我以前的帖子，我在那里创建了时事通讯。我按点赞数整理了推文数据，取了前五名，抓取了他们的数据。

这里唯一的区别是，我需要将所有内容放在一个异步函数中，并为每个承诺运行它。然后，该函数返回数组的数组。

```
async function organize_twitter_data() {
  try {
    let twitter_api_promises = await get_twitter_api_results(),
        tweet_data = [];

    twitter_api_promises.forEach(function(promise, i) {
      promise.then(function(value) {
        let images = [];
        const results = value.data.statuses;
        results.forEach(function(entry, i) {

          if (entry.entities.media !== undefined) {
            const newEntry = {
              filename: 'Wallpaper #' + i,
              path: entry.entities.media[0].media_url_https,
              likes: entry.favorite_count,
              cid: 'https://pbs.twimg.com/',
              img_tag: '<img src='+ entry.entities.media[0].media_url_https +' />'
            };

            images.push(newEntry);
          }
        });

        images.sort(function(a, b) { return b.likes - a.likes; });
        tweet_data.push(images.slice(1, 6));
      });
    });

    let all_promises = await Promise.all(twitter_api_promises);

    return tweet_data;
  }

  catch(error) { console.log(error); }
} 
```

Enter fullscreen mode Exit fullscreen mode

结果是一个包含三项的数组。每个都包含一个帐户中排名前五的 tweets 的另一个数组。这是我需要的最简单的数据形式。

对于这个文件来说，这是一个很好的停止点，因为我希望每个文件都有一个单一的、特定的目的——查找所有的 Twitter 数据。剩下的就是用`module.exports`导出它。

```
module.exports = {
  accounts: TWITTER_ACCOUNTS,
  data: () => organize_twitter_data()
} 
```

Enter fullscreen mode Exit fullscreen mode

您可能想知道为什么我还导出了 twitter 帐户数组。我需要这些信息作为邮件正文，导出这些信息可以避免在很多地方更新。这将在下一节详细解释。

### 构造邮件

我通过 GMail 发送电子邮件的代码没有改变，所以我将把重点放在电子邮件的新方式上。这是在一个单独的文件中完成的，第一步是导入所需的内容。

```
const twitter = require('./get_twitter_data'),
      send_email = require('./mailer'); 
```

Enter fullscreen mode Exit fullscreen mode

我当时处于一种“功能”的思维定势，于是我就思考有哪些任务可以分解成自己的功能。这个文件有两个主要任务:

1.  根据 Twitter 数据创建电子邮件正文
2.  用邮件程序发送电子邮件

我的第一个版本将这些放在一个函数中，但是我意识到它们可以是分开的。发送电子邮件将是异步的，因为它依赖于等待数据。但是制作邮件正文要简单得多——遍历数据来制作一个 HTML 对象。它的核心不是异步的。

所以第一个任务可能是一个普通的指针函数，从数组的数组创建一个三列的电子邮件布局。

```
const create_email_body = (email_body_data) => {
  let email_body = '';

  email_body_data.forEach(function(account, i){
    email_body += `<div style="width: 33%; float: left; padding: 8px; box-sizing: border-box;"><h2>${twitter.accounts[i]}</h2>`;

    account.forEach(function(photo, index){
      email_body += '<h3>Wallpaper #' + (parseInt(index) + 1) + ' at ' + photo.likes + ' likes</h3><img style="max-width: 100%; height: auto;" src="' + photo.path + '" >';
    });

    email_body += '</div>';
  });

  return email_body;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的`twitter.accounts[i]`在第一个`forEach`循环中，所以帐户名称与它们的壁纸相匹配。

有了这个独立的功能，异步发送邮件就变得简单多了。

```
async function send_newsletter() {
  try {
    const email_body_data = await twitter.data(),
          email_body = create_email_body(email_body_data);

    send_email(email_body);
  }

  catch(error) { console.log(error); }
} 
```

Enter fullscreen mode Exit fullscreen mode

它等待 Twitter 数据，用它创建电子邮件正文，并使用邮件程序发送正文。这很容易维护，而且这个函数是导出的，所以任何文件都可以用它来发送时事通讯。

```
module.exports = () => send_newsletter(); 
```

Enter fullscreen mode Exit fullscreen mode

### 通迅完成！又来了！

我又一次覆盖了很多，这甚至不是重构的全部。我还使用了节点环境变量来保护我的 API 键，并使用了一个 Express 服务器来代替 Heroku 的调度任务。总的来说，refactor 让我涉足了很多我以前只读过的领域——更多的 ES6 语法、async/await 和 Heroku 应用程序配置。

但就像我第一次写这个程序时一样，最大的一步是远离纯粹的前端，推进更高级的 JavaScript。我已经不再关注全栈，这意味着更深入地挖掘 JavaScript 的奇迹/恐怖之处，比如节点和 React 之类的框架。更多像这样的项目将帮助我混过所有的新信息，并将其具体化为真实的东西。

如果那些项目都是围绕动漫等书呆子话题？那只是奖金。