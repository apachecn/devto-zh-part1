# 我从没有计划一个网络应用中学到了什么(从开始到结束)

> 原文：<https://dev.to/jenc/what-i-learned-from-not-planning-a-web-app-from-start-to-finish-14a>

[![important men dot com gif](img/e2fd2042feef47fc01a47e49161e1204.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7W7I4_L8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://importantmen.com/img/importantMen.gif)

我最近启动了[重要人物](http://importantmen.com)，这是一个我已经拍摄了两年的艺术项目。

我在十月份部署了，但是维护和调试拖了一个多月。一部分是因为缺乏经验，另一部分是因为在多个截止日期上过度紧张。

该网站以在我生活中扮演重要角色的男人的风格化照片为特色。前端是静态的超光速粒子；后端是 Node/Express，Sendgrid API，用于接收表单输入和发送嵌入表单的电子邮件，所有记录都在 MLab 上的 MongoDB 中收集，后端托管在 Heroku 上。

在与设计师进行了一些评论后，我在 InVision 中制作了模型，但最后我的时间非常紧迫，我花了更多的时间来处理 api 响应并使项目适应 Heroku，而不是检查错误。

事后看来，我过于专注于匹配我的设计，并使网站看起来比我以前的任何工作都更时尚。但是我完全忽略了在[http://www.importantmen.com/matt/](http://www.importantmen.com/matt/)
设计问答表单的 api 行为，我希望这篇文章能帮助别人避免我的错误。

# 前端与后端解耦。

只要数据以 JSON 的形式返回，我就可以随心所欲地操作它，无论我在哪个 url 端点引用 js 文件并发出获取请求。一年多前，在 Angular immersion 课程中，我了解到路由应该服务于端点处的特定部分或文件(即'/'应该使用 index.html 等进行响应)。路由用于来回发送数据。路由用于来回发送数据。我不得不一直记着这件事。

# 先把基本功能弄下来。一次调试一件事。

不要用 StackOverflow 的解决方案来拼凑代码块(我在压力下会这么做)，制作小的测试应用程序并检查每一行代码对构建一个功能来说是有意义的，这比编写 10 行在几个地方中断的意大利面条代码更有帮助。我很想添加越来越多的功能，比如时间戳和 Oauth 登录来进行评论(在我看来，对于这样一个小项目来说，这是一个更大的障碍)，但我必须对我的能力持现实态度。

# 不能按照自己的冲刺计划来。

...因此，我可能会选择最有成效和最吸引人的任务清单，我绝对会重访。对我来说，这就是特雷罗。由于我有多个项目正在收尾，所以将所有事情都转储到 trello 上的几个专栏中，并将完成的事情移到“done”栏中，这要现实得多。

[![My trello task list](img/d941dbbcf66ab6429ba232b1d68af338.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VcQWMLtx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jtzoqhabfokvuglipemm.png)

# 设计 API 时要考虑逻辑视觉效果

如果不详细说明我的视觉导向思维如何只考虑一组结果，我想我无法解释这一点，但实际上，实现这些结果需要在后端代码中包含更多的条件和查询。

在显示已回答问题的情况下，我没有想到:

1.  当对某个问题的回答发布后，通过电子邮件通知用户
2.  只显示既有问题又有答案的记录，这样看起来就不会有问题没有得到回答或者 get 请求不起作用
3.  有这么多向下切换的标签来揭示页面上的基本信息是不必要的(极简主义者对功能的痴迷)
4.  包括加载器或某种视觉提示，但这也可能是过度的

[![Screenshot showing test of a fetch request response](img/21b098c26f0baa77aec3c047671c4057.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rHLg0YdN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/od2429phaoag50jmbe3v.png) 
要弄清那些“未定义”的真相

# 决定你的工具，在你进入计划的那个部分之前阅读它的文档。

我没有考虑过部署，只有 AWS buckets 的工作知识。在发布前夕学习如何配置服务器太冒险了，所以 Heroku 看起来是个不错的选择，因为我已经熟悉了 git。

# 不要浪费时间去学习一个新的框架

(特别是在最后期限的时候)，如果你还没有准备好在生产中使用它。

对于少于 10 页的网站来说，这可能只是额外的膨胀，并引入不必要的耗时的学习梯度。

[![Disastrous unlinked open graph image Facebook sharing](img/6a1f6220482cc87663f556c2dcc04c48.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ea3Jr93t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a130fg5c286jeemg1w2g.png) 
上图:部署后数周内许多恐怖事件之一。

# 总是在本地测试

如果在部署到 Heroku 后出现问题，恢复到在本地主机上进行测试，并建立一个虚拟页面(“我们正在处理”)。冲动的挫折测试意味着我在 Heroku 上有太多的版本，以至于我忘记了我对后端代码做了什么修改。MongoDB 的 Console.logs 也将更易于阅读。(如果我在这一点上错了，请纠正我)

# 付费玩很烂，但一致性可能值得。

我不知道我的一个获取请求的查看时间长达 15 秒！

如果你需要闪电般的速度工作，那么购买 Heroku 爱好等级可能是值得的。我听说过所有这些关于在 Heroku 上免费托管一个小项目的好事情，但是如果期望的行为因为服务器[在半小时不活动后进入睡眠](https://devcenter.heroku.com/articles/free-dyno-hours#dyno-sleeping)并需要被请求“唤醒”而闲置，那就毫无意义了。我听说有人写脚本每半小时唤醒一次服务器，这样它就不会进入睡眠状态。对于有压力的人来说，似乎有很多额外的工作要做。

[![Screenshot of menu item highlighted not in sync with image at scroll position](img/bb6e47c7acf4f04c3151956750780ea2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--uI1aSpDa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7h0jwecouranu74wxm1j.jpeg)

# 给自己充足的时间乱起来。

...大概是最后冲刺的 1.5 倍。

我没有，所以我最终启动了一个大约 80%功能的项目，然后花了一个月的时间推迟推广，直到我有了完整的问答功能。事后看来，我应该制定一份预部署清单，以完成如下收尾工作:

*   创建 robots.txt 以允许网页抓取和索引
*   测试脸书和 Twitter 的开放图形元标签，以确保链接共享工作的图像和标题预览
*   让多个用户测试网站链接
*   使用 chrome devTools 检查性能时间
*   确保人们能够浏览链接和输入字段
*   检查页面在不同设备上的外观
*   确保我可以通过输入或按钮切换焦点(可访问性)

这个[清单还可以继续](https://codeburst.io/the-front-end-checklist-8b2292fdda44)，但这是我真的希望我做的几件事。

# 在申请工作之前，对你的项目和投资组合进行防爆检查。

当它直播时，它是公开的，任何不工作的可能都不会很好地反映我作为一个开发人员和职业生涯中期的数字艺术家。我真的不得不抑制我的职业绝望。

感谢你阅读我的 nooby 思想。您的一些预部署清单和工作流是什么？