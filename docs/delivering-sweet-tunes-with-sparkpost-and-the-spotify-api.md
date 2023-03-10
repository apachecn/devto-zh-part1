# 通过 SparkPost 和 Spotify API 传递甜美的音乐

> 原文：<https://dev.to/sparkpost/delivering-sweet-tunes-with-sparkpost-and-the-spotify-api>

关于我的工作，我最喜欢的事情之一是利用现有的 API，并找出将它们与 SparkPost 混合和匹配的方法，以创建酷而有趣的应用程序。感谢我们的朋友[托德座右铭](https://twitter.com/toddmotto)，有一个 [Github repo](https://github.com/toddmotto/public-apis) 充满了公共 API 供我选择。使用这个 repo 中的 API，我创建了像 [Giphy Responder](https://github.com/aydrian/giphy-responder/) 这样的应用程序，以及许多其他没有成功移植到 Github 的应用程序。

SparkPost 最近赞助了纽约 Spotify 总部举办的[曼哈顿新闻](http://manhattanjs.com/)。这启发了我去看一看 [Spotify Web API](https://developer.spotify.com/web-api/) ，并想出一些我可以在 meetup 上演示的东西。他们的 web API 允许你做很多事情，比如搜索歌曲，获取艺术家和专辑的信息，管理播放列表等等。考虑到这一组功能，我如何将它与发送或接收电子邮件结合起来创建一个引人入胜的演示呢？

我喜欢音乐。我在大学时是朋克/斯卡曲风 DJ。当我拥有一辆车的时候，我会在里面唱歌(当我租一辆车的时候，我仍然这样做！).自 2011 年以来，我一直是 Spotify 的高级会员。现在我住在纽约，大部分时间都在地下旅行，所以我非常依赖我的离线播放列表。但问题是:我既不时髦也不酷，而且因为我不再听收音机，我不知道很多新音乐。这通常会导致我坐在地铁车厢里听 2000 年代早期的 emo 乐队，或者在听《一个新大脑》的演员录音时独自哭泣。

所以是的...我需要建议。Spotify 有很好的社交体验，但遗憾的是，并不是每个人都有 Spotify。但是如果你能把歌曲通过电子邮件发送到一个公共的协作播放列表中，那不是很酷吗？我很确定每个人都可以使用电子邮件。这也是为一个事件创建播放列表的好方法。所以我着手创建了 [JukePost](https://github.com/aydrian/jukepost) 。

这个想法很简单。首先，我会创建一个入站域(listen.aydrian.me ),它允许我向{playlist}@listen.aydrian.me 发送一封电子邮件，其中包含歌曲列表。(注意:{playlist}必须是现有的公共协作播放列表。)然后我会使用 Express.js 创建一个 Node.js 应用程序来处理 relay webhook，搜索歌曲并将其添加到指定的播放列表中，并回复一个确认，其中包括添加的歌曲和播放列表的链接。

## 网钩，你要把它们都抓住！

对于这个应用程序，我决定使用实时 noSQL 数据库 [Firebase](https://firebase.google.com/) 。我喜欢在我的很多演示应用中使用它，因为它让接收 webhooks 变得极其容易。它甚至会在你的应用不运行时接收它们。你只需要将你的 webhook 的目标设置为你的 Firebase URL + store name + .json。

因此，让我们设置一个入站域，并创建一个中继 webhook 来指向一个 Firebase 数据库。我将使用 [SparkPost 节点 CLI](https://github.com/SparkPost/node-sparkpost-cli) ，但也欢迎您使用自己喜欢的方式访问 SparkPost API。

*   为您的入站域设置 MX 记录。我会用 listen.aydrian.me
*   创建您的入站域名:`sparkpost inbound-domain create listen.aydrian.me`
*   为您的入站域创建一个中继 webhook，目标是您的 Firebase URL。我将使用`https://jukepost.firebaseio.com/raw-inbound.json`

```
sparkpost relay-webhooks create --name JukePost --target https://jukepost.firebaseio.com/raw-inbound.json --match.protocol SMTP --match.domain listen.aydrian.me 
```

Enter fullscreen mode Exit fullscreen mode

此时，您应该能够向`{anything}@listen.aydrian.me`发送电子邮件，并在`raw-inbound`下看到一个条目。

## 播放播放列表

现在，我们正在捕捉传入的电子邮件，我们需要一个代理应用程序来解析数据，处理与 Spotify 的交互，并触发一封回复电子邮件。

首先，我们需要使用 Oauth 2.0 对 Spotify 进行认证。这是我第一次这么做，幸运的是，我找到了`spotify-web-api-node` [npm 包](https://github.com/thelinmichael/spotify-web-api-node)和一个很棒的[博客](http://jkaufman.io/spotify-auth-react-router/)，它帮助我创建了让一切运转起来所需的`login`、`callback`、`refresh_token`路线。一旦应用程序通过认证，我们就可以提取用户的公共播放列表，过滤掉协作播放列表，并保存起来以备后用。

现在我们可以使用`firebase` [npm 包](https://www.npmjs.com/package/firebase)来监听新的入站消息并相应地处理它们。因为 Firebase 会实时通知我们新消息，所以我们可以设置一个监听器。

```
const firebase = require('firebase')
const relayParser = require('./relay_parser')

firebase.initializeApp({
  apiKey: process.env.FIREBASE_API_KEY,
  authDomain: process.env.FIREBASE_AUTH_DOMAIN,
  databaseURL: process.env.FIREBASE_URL
})

const ref = firebase.database().ref('raw-inbound')
ref.on('child_added', snapshot => {
  snapshot.forEach(item => {
      const data = relayParser.processRelayMessage(item.val().msys.relay_message)
      // Search for Tracks, Add to Playlist, Send Email
}) 
```

Enter fullscreen mode Exit fullscreen mode

你可以看一下 [relayParser.js](https://github.com/aydrian/jukepost/blob/master/server/relay_parser.js) 看看我是如何从中继消息中抓取相关数据的。根据我们从邮件正文中解析的信息，我们现在知道是谁发送了邮件，将歌曲添加到哪个播放列表，以及要搜索哪些歌曲。现在，我们已经拥有了查找歌曲并将它们添加到播放列表所需的一切。请务必将歌曲信息添加到替换数据对象中，因为我们将在确认电子邮件中使用它。

[![Sample Confirmation Email](img/1a0b73d6ef79598ed8788e331f877a51.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--nEYmMOZI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mavjkefssrr7ffmigyyj.png)

我选择用我的[确认邮件](https://github.com/aydrian/jukepost/blob/master/resources/JukePost_Email_Response.png)变得有点花哨。我决定使用一个存储的模板，该模板将返回一个到播放列表的链接，以及与艺术家、封面艺术和 30 秒样本一起添加的歌曲。为了方便起见，我将我的[模板 html](https://github.com/aydrian/jukepost/blob/master/resources/add_template.html) 和一个[样本 json 对象](https://github.com/aydrian/jukepost/blob/master/resources/substitution_data.json)放在 github repo 的 resources 文件夹中。

这是一个有趣的小项目，演示非常成功。想自己试试吗？向[spark post @ listen . aydrian . me](//mailto:sparkpost@listen.aydrian.me)发送电子邮件，主题行为`Add`，然后以{title} by {artist}格式将您喜爱的歌曲添加到正文中。让我们一起建立一个很棒的播放列表。

这只是这个应用程序能做什么的冰山一角。有一个很酷的添加创意吗？请随意[创建问题](https://github.com/aydrian/jukepost/issues/new)或[提交拉动请求](https://github.com/aydrian/jukepost/compare)。

另外，想在入站电子邮件处理方面做得更多吗？看看我关于入站邮件和其他你可以用它做的很酷的事情的帖子。

这篇文章最初发表在 [SparkPost 博客](https://www.sparkpost.com/blog/spotify-api-jukepost/)上。