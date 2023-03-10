# 非英语国家程序员英语水平的现实？

> 原文：<https://dev.to/saki7/reality-of-programmers-english-skill-in-non-english-speaking-countries-a4j>

英语是程序员事实上的标准技能。人们使用英语进行交流、申请传票、联系技术支持、文档和代码注释。

重要的是，大多数开源软件是由英语社区驱动的。我仍然观察到一些例外，例如，中国防火墙互联网背后的技术/生态系统。他们使用中文，因为这些是明确针对中国用户的。

基本上，我不想使用非英语语言编写的软件，因为我觉得他们在说“我们不尊重事实上的标准技能。”

## 中国现实(？)

我提到了中国互联网，那么我们来看看他们的实际代码。

```
 int64_t current_term_;      /// 当前 term
    std::string voted_for_;     /// 当前 term 下投的票
    LogDB* log_db_;             /// log 持久存储
    int64_t log_index_;         /// 上一条 log 的 index
    int64_t log_term_;          /// 上一条 log 的 term 
```

Enter fullscreen mode Exit fullscreen mode

[https://github . com/Baidu/bfs/blob/2f 69 b 86 f 5251 a 642 a 648582 DFC 17 dbe2a 871800 e/src/name server/raft _ node . h # L84-L88](https://github.com/baidu/bfs/blob/2f69b86f5251a642a648582dfc17dbe2a871800e/src/nameserver/raft_node.h#L84-L88)

我只是对这些用中文写的代码注释感到惊讶。我的观点是，这个项目不是一些匿名同学上传的爱好项目。这是一个由大公司开发的大框架(百度公司是中国排名第一的互联网搜索引擎)。

### 编辑:代码

让我解释一下这段代码。看一下第二行，

```
 std::string voted_for_;     /// 当前 term 下投的票 
```

Enter fullscreen mode Exit fullscreen mode

这个变量是什么意思？

良好的...变量名为“voted for ”,所以它应该包含目标的名称作为字符串。现在来看看评论:

```
/// 当前 term 下投的票 
```

Enter fullscreen mode Exit fullscreen mode

如果你有一些关于野生源代码的经验，你可能已经注意到评论中的 **`term`** 这个词。这实际上是一个重要的暗示；`voted_for_`变量实际上指的是在前一行的“当前任期”下投票的投票目标。

嗯，你*可能*在不懂任何中文的情况下猜出这个真实的意思，但是如果你不知道`当前 term 下投的票` == `the vote cast under the current term`这个事实，那就真的很难了。

## 日本现实

我住在日本。这一节是基于我的个人经历。包括日本推特时间线上的每日观察。

日本历来对外来文化过敏。自公元 1639 年至 1854 年，日本人甚至将整个国家与世界隔离开来(见[https://en.wikipedia.org/wiki/Sakoku](https://en.wikipedia.org/wiki/Sakoku))。

日本人糟糕的英语技能主要是基于这种“我害怕外国文化”的东西。我们确实把英语纳入了义务教育，但是我们在现实生活中不会说英语(因为所有的东西都是用日语写的&这太正常了)。

日本程序员也不例外。自从互联网出现以来，这种“糟糕的英语技能”一直是个问题，但由于开源软件的兴起，这个问题变得越来越严重。

一些著名的日本科技公司愿意解决这个问题。最受欢迎的网上购物网站之一乐天公司已经将英语定为公司官方语言。这一事件被称为“英语化”(你可以在他们的官方公司网站[https://corp.rakuten.co.jp/careers/culture/](https://corp.rakuten.co.jp/careers/culture/)上看到)。

“英语化”已经失败了，很明显(如你所见，他们的公司网站是用日语写的)。这个失败太讽刺了，甚至正常的日本人(即非程序员)都会提到他们的失败。

## 你的国家？

日本人经常谈论“英语能力差”的问题。也许太多了。在 Twitter 上，我看到一些随机的日本人说‘日本程序员很烂，因为他们的英语水平很差。’这些推文在程序员中迅速传播；最终收到超过 2k 的转发/赞。

这种情况在日本以外的地方“正常”吗？你的国家怎么样？英语国家如何看待这个问题？