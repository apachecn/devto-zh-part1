# 本周代码，10 月 17 日

> 原文：<https://dev.to/ben/this-week-in-code-october-17th>

如果你错过了其中的任何一个，这里有一些过去一周最值得注意的链接。

[脸书发布了 JavaScript 的 Yarn package manager】它被称为快速、可靠、安全的替代 npm 客户端。它与 npm 注册表兼容。这里是来自 npm 的](https://code.facebook.com/posts/1840075619545360)[声明](http://blog.npmjs.org/post/151660845210/hello-yarn)，这里是来自 Bugsnag 的一些[初步印象](https://dev.to/bugsnag/replacing-npm-with-yarn)。

以下是 Bugsnag 测试的性能数据

| 命令 | 时间(秒) |
| --- | --- |
| `npm install` | 155 秒 |
| `yarn`(冷缓存) | 53s |
| `yarn`(热缓存) | 13s |

[GitHub 和 GitLab 审查开发者的研究数据](https://gwillem.gitlab.io/2016/10/14/github-censored-research-data/)开发者的包含 Javascript 恶意软件的网站列表。GitLab 随后发表了一份[声明](https://about.gitlab.com/2016/10/15/gitlab-reinstates-list-of-servers-that-have-malware/)，承认删除名单是一个不明智的举动，并恢复了名单。

Visual Studio Code 1.6 发布这是一个相当广泛的发布，包括 JavaScript 和 TypeScript 的语言改进。

[成为首席技术官](https://juokaz.com/blog/becoming-a-cto)这是对首席技术官这一头衔所包含的内容，以及做好这份工作需要什么的一个很好的概述。这里的一切都是主观的，但这是进入这个位置的概念性路线图。

[Tesseract.js](https://github.com/naptha/tesseract.js) 支持 62 种语言的纯 Javascript OCR

### 举几个例子:

```
// simple example
Tesseract.recognize(myImage)
.then(function(result){
    console.log(result)
})

// if we know our image is of spanish words without the letter 'e':
Tesseract.recognize(myImage, {
    lang: 'spa',
    tessedit_char_blacklist: 'e'
})
.then(function(result){
    console.log(result)
}) 
```

Enter fullscreen mode Exit fullscreen mode

[阿达·洛芙莱斯日:她是谁——为什么我们应该记住她](http://www.independent.co.uk/news/people/news/ada-lovelace-day-who-she-was-and-why-we-should-remember-her-a6692051.html)周二是阿达·洛芙莱斯日，庆祝一位计算先驱和 STEM 女性的所有贡献。

[机器人在选举中发挥的作用比人们意识到的更大](https://dev.to/ben/bots-are-playing-a-bigger-role-in-the-election-than-people-realize)让我们看看软件是如何影响选举周期中的对话的。

[如何成为一名编译器——用 JavaScript 制作一个编译器](https://medium.com/@kosamari/how-to-be-a-compiler-make-a-compiler-with-javascript-4a8a13d473b4#.43l9klzm4)从制作编译器中学到的东西，既有趣又有启发性。

[Cheese:来自物联网摄像头的大规模 DDoS 攻击快照](https://blog.cloudflare.com/say-cheese-a-snapshot-of-the-massive-ddos-attacks-coming-from-iot-cameras/)来自 Cloudflare 的关于两起 DDoS 攻击的数据，它们是 DDoS 新趋势的完美例子。

[认真对待 PHP](https://slack.engineering/taking-php-seriously-cf7a60065329#.kxl9lcmnx)Slack 为什么选择用这种语言来构建一个新项目？你应该吗？

## 本周精彩播客片段

[Kubernetes，Containers，和 Kelsey Hightower 一起去](https://dev.to/gotime/20-kubernetes-containers-and-go-with-kelsey-hightower)

[利用 Octopus Deploy 和 Damian Brady 协调和自动化部署](https://dev.to/hanselminutes/orchestrating-and-automating-deployments-with-octopus-deploy-and-damian-brady)

【Gene Kim 的 DevOps 手册

## #发展讨论

本周的[# dev discuse](https://twitter.com/hashtag/DevDiscuss)Twitter 聊天是关于“进入 Go”，[这里是重点](https://twitter.com/i/moments/786024650660716545)。

本周话题的投票[仍未结束](https://twitter.com/ThePracticalDev/status/787820175387230208)，选择很紧。讨论将于周二(明天)美国东部时间晚上 9 点(UTC-0400)举行。