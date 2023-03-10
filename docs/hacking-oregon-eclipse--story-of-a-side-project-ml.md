# 黑客入侵俄勒冈 Eclipse——一个兼职项目的故事

> 原文：<https://dev.to/picsoung/hacking-oregon-eclipse--story-of-a-side-project-ml>

[![](img/578d70dfe2c6fb9a6d6f6b434974f35a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BTewcNo5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l57s3450czcdiltf09vf.gif) 

<figcaption>日食项目截屏</figcaption>

最近，我对新的“流行的”Javascript 框架很好奇: [Vue.js](http://vuejs.org) (我知道这是一种赘述)。我只是需要一个附带项目的想法来发现这个每个人都在谈论的新的闪亮玩具。

一天晚上，在酒吧里，当我和我的同事 Josh 喝着上好的 IPA 时，我有了这个项目的想法。我们在谈论即将到来的美国日食，以及去哪里观看。

遗憾的是，我个人并没有计划去欣赏日食那周在俄勒冈州举行的众多[嬉皮士聚会](http://oregoneclipse2017.com/)中的一个。但是我确信我能够在我居住的加利福尼亚附近欣赏到这种罕见的现象。

通过一些基本的谷歌搜索，我找到了美国国家航空航天局的官方网站，上面列出了该国组织的所有活动。好消息:天文学家、科学博物馆和美国国家航空航天局将为每个人举办大量的活动来欣赏日食。

[![](img/d2ed34e10f54debc9dcbfbe986247c61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x_pPvuFU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/749/1%2AEHYetAr3Yx_nmbf1682jRQ.png)

<figcaption>NASA 网站上显示的事件地图</figcaption>

不幸的是，NASA 网站上的用户体验并不令人兴奋。你必须指向并放大地图才能找到你周围的不同事件。我认为应该有更好更好的方法:这就是为什么我建造了[🌚🌞。ws](https://%F0%9F%8C%9A%F0%9F%8C%9E.ws) 。

[![](img/ef7047726625b7622234e19e5af0b967.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZEWEZTgE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARHLAkdTbworb0QavR_eLTA.png)T3[🌚🌞。ws](https://%F0%9F%8C%9A%F0%9F%8C%9E.ws) 体验

### 提供更好的搜索体验🔍🗓

我的主要目标是提供一个愉快的搜索体验，人们可以输入他们的位置，应用程序会显示附近的活动。

说到搜索，我选择的工具是 [Algolia](http://Algolia.com) 。他们提供 API 和 SDK，在像 [Twitch](http://twitch.tv) 、 [Product Hunt](http://producthunt.com) 或 [Hacker News](http://news.ycombinator.com) 这样的网站中提供直观的搜索。

为了能够使用他们的解决方案，我需要获得所有的事件数据。通过检查 NASA 网站的源代码，我发现了他们用来在地图上填充事件的谷歌电子表格。我只需要下载并把这个文件转换成 JSON。

我在 [Algolia](http://algolia.com) 上创建了一个事件索引，在那里我将存储所有的事件。为了能够执行基于地理的搜索，我将 location 字段重命名为 _geoloc，就这样。我已经可以用经纬度坐标搜索地球上某个特定点周围的事件。

然后，我使用了 [Algolia Places](http://community.algolia.com/places/) ，而不是一个人们可以自由填写地址的字段。这是一个自动完成地址或城市名称的文本字段。该工具使用开放的街道地图数据，因此非常准确。

[![](img/9a5e61be7ac1a8f46c5abd69d56d16b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--npGYPpC_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0mEqCkEnJRzzlWvZE-q_hA.gif) 

<figcaption>阿哥利亚地名体验</figcaption>

使用这个工具，用户可以开始输入 SAA，工具会提示旧金山。在一个回调函数上，应用程序将获得关于这个位置的所有信息，比如它的纬度和经度，以及它的州或邮政编码。

这就是我如何在几个小时内获得我的第一个 MVP。捕捉输入字段上的更改事件，接收关于位置的数据，对事件索引执行地理搜索并显示结果。

### 获取局部日食情况🌒🌘

我对我的 MVP 很满意，但是我想添加更多关于日食当地环境的信息。知道它会持续多久不是很酷吗？或者什么时候会是最大值？

天真地，我认为日食在同一时间在各地发生。所以我只需要把日期转换成当地时区。

[https://www.youtube.com/embed/xoMgnJDXd3k](https://www.youtube.com/embed/xoMgnJDXd3k)

看来我忘记了基本的物理知识…

月球和地球都在同时运动，所以月球在地球上的影子也在运动。

请查看此动画，以便更好地了解它:

> ![](img/15103cda76dcbeeb0e3304a03b3e803a.png)美国国家航空航天局@美国国家航空航天局![](img/4d9c44713c216584b3d48ff3455cbb68.png)你知道你需要特殊的太阳滤光眼镜来观看 8 月 21 日的太阳 [#Eclipse2017](https://twitter.com/hashtag/Eclipse2017) 吗？了解更多:……[twitter.com/i/web/status/8…](https://t.co/ZIUptacr1Y)2017 年 7 月 23 日下午 18 点 18 分[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=889187888797286401)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=889187888797286401)1434[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=889187888797286401)3043

因此，我必须找到一种方法来获得所有这些当地的情况，因为我无法猜测他们。

嗯，如果我对你 100%诚实的话…我可以…通过我的研究，我已经了解了[贝塞尔元素](https://en.wikipedia.org/wiki/Besselian_elements)。通过一些计算，你可以预测地球上任何一点发生日食的所有步骤。当你意识到它是在 1820 年发现的，而其他文明在几千年前就已经使用了类似的技术时，这听起来就更疯狂了。

我找到的唯一文档充满了无穷无尽的方程式，我没有信心用 Javascript 来处理它们…

我终于发现了一个来自美国海军天文台的 API 可以帮我做到这一点。这个 API 给出了日食不同阶段的时间，以及星等、太阳方位角和一些角度。

我又一次天真地认为，有了这些数据，我就有足够的东西来绘制一幅美丽的太阳月影图。

[https://medium . com/media/796 beafd 8 fbafbb 2d 36171 c 22050 f 269/href](https://medium.com/media/796beafd8fbafbb2d36171c22050f269/href)

我花了两天时间试图找到做这件事的正确方法。我用了高中时记得的所有三角学。我梦里充满了疯狂的计算。经过多次尝试，我无法得到其他网站显示的结果。我不得不放弃😩。

> 如果有人有线索，我很乐意尝试记录如何根据纬度和经度在 SVG/CSS 中显示日食。
> 
> 此刻，可视化从[时间和日期](http://timeanddate.com/eclipse/in/usa/)传来。
> 
> ### 寻找域名💻🔗

你可以很容易地打赌，这个特殊事件的所有好域名都被有商业头脑的人抢注了，在众多弹出广告中给你提供的信息很少。

我尝试了日蚀、事件、我们周围的变化；但是我什么都不喜欢…

直到几天前, [Domainji](https://www.producthunt.com/posts/domainoji) 出现在产品搜索上。

就是这样:我必须得到一个表情符号域名！🎉

我已经了解了很多关于[双关语代码](https://www.punycoder.com/)，并决定购买简单的[🌚🌞。i❤.](http://%F0%9F%8C%9A%F0%9F%8C%9E.ws)[上的 ws](https://t.co/cE1K2b3ef2) ws 。

> 你看到月亮是如何有食欲地看着太阳的吗？这是月蚀期间发生的事情。

当涉及到开放图形标签时，表情域是一种痛苦。例如，脸书 OpenGraph 调试器不承认他们是有效的网址。在调试网站的这一部分时，我度过了一些有趣的时光。

### Vue 体验👨‍💻👍

你还记得我在这篇文章的开头谈到了 [Vue](https://vuejs.org) 吗？

是啊，那又怎么样？

为了开始，我读了马蒂亚斯·哈格的教程，它给了我一个很好的工作基础。这对于我的用例来说很直观。我没有把我的应用程序切割成组件，直到我确信一切都在一起工作。

我很高兴地发现了很多资源和现有组件，如 [Places](https://github.com/Gomah/vue-places) 、 [Tooltip](https://github.com/vue-bulma/tooltip) 或 [Prerender](https://www.npmjs.com/package/prerender-spa-plugin) (用于 opengraph 标签)。我还发现[布尔玛 CSS](http://bulma.io) 比 Bootstrap 好用。

你可以在这里找到项目[的代码:所以如果你对如何使它更“ **Vuesque** 有任何意见，请告诉我。](https://github.com/picsoung/eclipse2017)

我希望你喜欢这个黑客，随时通过反馈和功能要求。

请安全欣赏日食！

* * *