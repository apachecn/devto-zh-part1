# 4 周内从创意到 400 万的页面浏览量

> 原文：<https://dev.to/mubashariqbal/from-idea-to-4m-page-views-in-4-weeks>

<small>*原载于[黑客正午](https://hackernoon.com/from-idea-to-4m-page-views-in-4-weeks-622aa194787d)。*</small>

上周二，《机器人会取代我的工作吗？5 天后，我们已经有了 50 万访客和 400 万页面浏览量。

[![](img/10be31a398f3ccbc91adc3df562f46f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7WF-aa9O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2A_CCtffb7okTlKFUMHnBuew.jpeg)

如果说这超出了我们的任何预期，那将是一种保守的说法。这是一个项目如何开始和启动后的前 5 天的回顾。

### 后面的故事

就在一个多月前[丹恩·佩蒂](https://medium.com/@DannPetty)推出了[自由电视](http://freelance.tv/)。我断断续续做了 10 多年的自由职业者，所以我很乐意支持这部纪录片，并加入了 Slack 社区。

Dimitar Raykov 也加入了，并给我发了消息，我们开始讨论在一个项目上合作的可能性。我们决定从一些小而快的事情开始，看看我们可能会喜欢一起工作。

迪米塔提到了他的想法，即围绕 2013 年发表的一份关于通过机器学习和移动机器人实现工作自动化的可能性的报告建立一个网站。

我对 AI/ML 领域感兴趣已经有一段时间了，甚至参与创建了 [BotList](https://botlist.co/) 。

我们已经看到许多聊天机器人在工作中帮助人们，甚至在某些情况下取代他们，人力资源[和 T2](https://botlist.co/bots/filter?category=9)旅行类别就是很好的例子。

我们决定推进这个项目。

仅仅显示自动化风险的百分比，并不会带来非常有趣的用户体验，所以我们寻找有帮助的额外数据。我们通过[美国劳工统计局](https://www.bls.gov/)网站在报告中找到了更多关于这些工作的信息。我们可以下载工资数据和对雇佣人数的估计。

* * *

### 打造

迪米塔将处理设计和前端，我会处理后端和托管职责。

我们想让人们更容易找到工作，因为该报告只涵盖了 702 份工作，找到类似的工作将是一个重要的功能。我们决定用 [Algolia](https://www.algolia.com/) 来实现这个目标。他们的搜索匹配非常好(即使有拼写错误的单词)，同义词功能可以很容易地找到相关的工作。我们注册了试用版，并认为免费层将意味着我们能够免费运行该网站——稍后将详细介绍！

我们开始工作，我用 Bootstrap 在 LaravelPHP 上制作了一个快速原型，而 Dimitar 则负责设计。

设计完成后，Dimitar 在静态 html 页面中构建了页面，并将它们交给我进行集成。

我们在工作中添加了一些有趣的功能:

*   Algolia Instantsearch 为求职自动完成提供动力，为什么要重新发明轮子。
*   简单的工作页面链接复制和分享到 Twitter 和脸书。
*   自定义打开图形共享每个作业的图像。
*   每次访问的随机调色板。去试试吧！每次访问你都会被分配到十个调色板中的一个。

我们花了大约 3 周时间在晚上和周末工作。

我把网站建立在我为其他项目准备的数字海洋上，这个网站是实时的，只是没人知道。我们和几个朋友分享了这个网站，并得到了一些早期的反馈。

* * *

### 发射

鉴于我对[产品搜寻](https://medium.com/@producthunt)的喜爱，那似乎是启动我们项目的明显地点。我联系了[本·托塞尔](https://medium.com/@bentossell)，和他讨论了这个项目。他同意为我们打猎。Dimitar 创建了我们希望在网站上用于产品的缩略图，并准备了一些图像来显示一些功能，我们将这些图像传递给了 Ben。

该网站于周二(5 月 30 日)凌晨 3:15(美国东部时间)出现在[产品搜索](https://www.producthunt.com/posts/will-robots-take-my-job)上。我喜欢在猎物第一次被猎杀的时候在旁边，添加评论并回答任何直接的问题。既然我醒了，我决定也把这个网站发布到[黑客新闻](https://news.ycombinator.com/item?id=14443606)，迪米塔尔把它发布到[设计师新闻](https://www.designernews.co/stories/84124-will-robots-take-my-job)。

我们立即在所有三个网站上获得了牵引力。我们很快移到了产品搜索的第二位，我的帖子移到了[设计师新闻](https://www.designernews.co/)和[黑客新闻](https://news.ycombinator.com)的首页。当我准备睡觉的时候，我注意到客人们开始滴答作响。谷歌分析显示，我们有 48 人在网站上，这一数字跃升至 88，然后上升至 327。现在不要睡觉。

直到早上 5 点左右，我才终于上床睡觉，访客人数在 440 人左右达到峰值。

在我短暂的午睡后，我不得不起床并让孩子们准备好去上学，我看到我们在下一个网络上被写了。这使得我们的访问量更高，我们的网站有超过 500 名访客。

[![](img/64e0237018e789eedc7645df8d57a033.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WaVUPNKp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A9vN5Ujpd7ntIIv-P4DFxOg.png)

我们很快就超过了 Agolia 免费层的限制，升级到了他们的初级产品包。每月 100 万次手术，我们应该会做得很好。

迪米特开始联系记者，帮助传播关于网站的消息。取得初步成功，有助于构建外联，这将导致进一步的成功。

访问者持续上升，来自 TNW 的文章被整合到许多网站，包括 MSN 和 AOL。我们的访客人数激增至 822 人，然后在一天之内达到峰值，超过 1400 人。

#### 第二天

我们继续收到在各种网站上写的文章，最有趣的是不仅仅是在技术领域。亚利桑那州、德克萨斯州和华盛顿州的一般新闻网站都写了关于我们的报道，酒吧体育网站上的一个帖子导致访问量大幅增加。

我们的网站达到了 2000 名访客的高峰！

我们很快就接近了阿格利亚的首发阵容极限。我无法证明 300 美元升级到下一层的合理性，于是开始研究替代方案。我在推特上发布了这个情况，Algolia 回复了，并提出通过提高我们的使用限制来帮助我们。这节省了我们大量的时间和担心，来自 Algolia 的支持非常感谢！

Algolia 的完整搜索替代方案不会更便宜，而且工作量更大。用 Lucene 或 Elasticsearch 运行我们自己的服务器本来是一个选择，但是托管的成本和投入的时间并没有很好地利用我的时间。

我们最好的选择是切换到 Laravel Scout 的 [TNTSearch，不需要额外的服务器，在 Laravel 中围绕搜索编写 JSON API 会相对简单。谢天谢地，这些都不是必需的。](https://github.com/teamtnt/laravel-scout-tntsearch-driver)

#### 第三天

我们一整天都有非常稳定的访客，最终在红迪网上排名第一。

虽然我们以超过 5000 张选票结束了这一天，但不幸的是，我们没有成为 Reddit 的热门页面，也许下次吧！

#### 第四天

我决定最好升级服务器，我们得到了相当稳定的访问者，虽然服务器处理负载相当好，我想有一些备用容量，并确保页面加载速度非常快，为我们的访问者。

Laravel Forge 让设置服务器变得非常简单，我唯一担心的是给新服务器添加 SSL。我在原来的服务器上使用了 [LetsEncrypt](https://letsencrypt.org/) ，但是你不能这样做，除非你已经将你的 DNS 指向服务器。幸运的是，我记得我的[账户里有一些未使用的 SSL 证书。我订购了一个 SSL 证书，几分钟后新服务器就启动并运行了。](https://affiliate.namecheap.com/?affId=117123)

当我更新新服务器的 DNS 时，我祈祷好运。幸运的是，我们成功实现了零停机时间的迁移。

额外的容量派上了用场。虽然我们没有得到另一个大的游客激增，但平均每天更高。我们确实以 1600 名访客的峰值结束了这一天，这些访客似乎来自谷歌搜索。我们仍然不确定是什么导致了游客的激增。

#### 第五天

终于有时间喘口气了。周末导致较低的访问者水平，但是一天大约有 300-400 个访问者，我们仍然很高兴！

### 结果

尽管我们在发布日曾一度登上产品搜索主页的榜首，但我们最终还是排在了第二位。落后于安迪鲁宾的基本电话。

[![](img/5828868df27c816f712e7461d408dadf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QNf98BYp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2A4GkAM10tQihPJeZRa1fTuQ.png)

我们有接近 950 张选票，我希望在不久的将来第四次达到 1000 票俱乐部:)

我们在黑客新闻主页上停留了几个小时，我们在一个受欢迎的子 reddit 上停留了 3 天，并出现在许多受欢迎的网站上，包括:TheNextWeb、LifeHacker、Mashable、MSN、FoxNews、AOL、BarStoolSports、BusinessInsider、Geek.com、Vice、Gizmodo、BoingBoing 和 Fortune。

[![](img/d93194c10a88719be2bd2d59aeff9ac9.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--QwrvLr-Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Akl2CDgcuM-KkQuCjX2iSCQ.png)

截至上周六，仅 5 天时间，就有超过 50 万人访问了该网站，产生了超过 420 万的页面浏览量，平均每次访问超过 6 次。我们预计游客会放慢速度，即使是大幅下降也会给我们带来比预期更多的流量。

[![](img/9c3bd30c738b70b2dbe90daf76076486.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wxFtTdyX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AeZwXtF8KJ6B6Gw6klx2iIQ.jpeg)

* * *

### 吸取教训

仅仅因为你建立了一些小而快的东西，并不意味着它不会有大的影响。机器人会取代我的工作吗？无疑是我参与的最成功的个人产品发布会。

是的，我参与过更多高姿态、高流量的发布，但这些都涉及到大营销团队和大预算。我们是来自纽约北部的开发人员，来自保加利亚的设计师，没有预算。

#### 制造乐趣

迪米塔和我在建立网站的时候很开心，第一次一起工作，但这不是我唯一提到的事情。我们让我们的访问者觉得网站很有趣。我们给网站注入了一些乐趣，而不是枯燥乏味，只是显示数字。任何极有可能自动化的工作都会出现“你注定要失败”的信息。

[![](img/f170bff6b1657374bc364a1fae78ff92.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--b7WdJXc_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2As4ynWRJcYosV6I5b2GID7A.png)

#### 使其可共享

虽然这并不容易，但我们做到了让每份工作都有一个自定义的共享图像，所以当人们在社交媒体上分享他们的工作时，它会呈现他们特定的自动化可能性，而不是一个通用的图像。脸书和推特吸引了超过 10 万的访问者，我们认为这很大程度上是因为我们自定义的分享图片。

#### 可用

通过电子邮件，通过社交媒体，通过评论。我们试图回应任何人和任何有问题或评论的人。我们相信这会带来更多的文章、更多的分享和更多的投票。这需要时间，但是如果你不把你的时间奉献给你的项目，其他人为什么要这样做。

#### 心怀感激

迪米塔尔和我都非常感谢每一个写下、分享或刚刚访问过这个网站的人。没有你，这一切都不可能。

我们特别感谢 Algolia 的支持，我们可能需要再次讨论提高ðÿ˜的运营限制。

* * *

如果你没有机会去参观，机器人会取代我的工作吗？然而，现在就开始尝试吧。回来留下你的看法吧。

<small>*原载于[黑客正午](https://hackernoon.com/from-idea-to-4m-page-views-in-4-weeks-622aa194787d)。*</small>