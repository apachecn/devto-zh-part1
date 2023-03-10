# 我希望我知道如何在 AWS Lambda 中使用 MongoDB 连接

> 原文：<https://dev.to/vladholubiev/i-wish-i-knew-how-to-use-mongodb-connection-in-aws-lambda-c46>

几周前，奇怪的事情开始发生。来自所有环境的随机 Lambda 函数抛出错误。我的脸变白了。

> MongoError:cursor . next 的 next function(/var/task/node _ modules/MongoDB-core/lib/cursor . js:540:25)没有可用的连接

没有明确的模式。mongo connection 的相同代码正常工作了 11 个月，在 1 天就崩溃了。us-east-1 上 node.js 中 270℃函数生成的调用中有 0.0004%随机丢失数据库连接。

给你一些背景知识，Docker 和 Lambda 的区别在于后者处理环境状态的方式。

你已经学会了在应用程序启动时建立数据库连接。但是无服务器功能每次运行都是全新的。

[![](img/c791773536e7b3cc81b9c897d00ba9ff.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--hnRvu3ZI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AnCFcgUc3Gm7Tonm91UtoeQ.png)

当函数返回-时，所有后台进程被冻结。

车床功能开始-它们被恢复，数据库对象正在等待重用。

这种方法在一段时间内有效，直到它停止。甚至几个月前部署的功能也被感染了。与此同时，Docker 集装箱内的一队微服务没有任何问题地航行。看起来像个调试地狱，是吧？

我最终有三个嫌疑人:开发人员 me、MongoDB 和 AWS。当然不是我，我写的是零 bug 的完美代码。有时候。从来没有。

## 蒙哥

最明显的。为什么会失败？也许你承受不了这个负荷？你到底是不是[网秤](http://www.mongodb-is-web-scale.com/) *？

玩笑归玩笑，负载图表看起来还不错。中等系统使用率。尽管如此，这并没有阻止我打扰我的主机提供商。

[![](img/7752747a80de19d1fbefb5bdb9ee9050.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--9-f1l7bl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A5esKYZtkPYWBocHU1DQ3Mw.png)

甚至戴夫确认蒙哥负荷图是平坦的地球。继续前进。

## 节点

因为您不能调试功能，所以了解系统的唯一方法是使用 AWS X 射线。有点像兰姆达斯的拉链。

[![](img/ac7e1cc9eddaf212dcc8d412bef2ae5e.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vYmi_oFV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A3YLAYhz28BYMCngJOWPA6Q.png)

它非常有用，但是只跟踪对 aws sdk 和传出 http 端点的调用。对于 mongo 调用，您需要编写一些自定义代码，它只在 trace details 中可见，而不在上面的地图中。

`mongodb-core/lib/cursor.js:540:25`的 Stacktrace 让我看到了 [mongodb-core](https://github.com/mongodb-js/mongodb-core/blob/2.0/lib/cursor.js#L540) 驱动包中最近的一些代码。9 月 9 日犯下，10 月 10 日释放。一到两周我就可以更新 npm 依赖项，然后就搞定了。错误开始在日志中弹出的确切时间。

[![](img/ed3eb434b8685fad65073b46723b1854.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Thxu0sec--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AuinA0-myvpVNldauVO5vkA.png)

原来，与此同时，提交作者写了一篇文章[管理与 MongoDB Node.js 驱动程序](http://thecodebarbarian.com/managing-connections-with-the-mongodb-node-driver.html)的连接。洞察重联机制的本质ðÿ'ðÿ

它给了我一个想法，监听`reconnectFailed`事件来打印日志并大声失败。在等待随机错误再次出现时，我去了 RTFM。

[![](img/0cf7fb86930640d76cd9ecb787647ba5.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--qrWxPxJF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2Aj1V3mvb5RNEGLaQJIke8_g.png)

不幸的是，日志消息从未出现。所以也许我错过了一些基本的东西。糟糕的感觉。

## λ

我谷歌了一下“mongodb lambda”。

[![](img/465d20a48d7fefa4f3f427cac343d2e6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--WA5nBU1J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AhFu76lPwz6GTulLpurjnlw.png)

不错，mongodb.com，如果不是作者，谁会告诉你更好？

第[篇](https://www.mongodb.com/blog/post/serverless-development-with-nodejs-aws-lambda-mongodb-atlas)教程展示了如何在 Lambda 中缓存 db 连接。

[![](img/08feb5af6b97d9eb285e52e9099b619b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--8gO3pJhv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2ArCTJEBKXQJkFpCCC8T-Ong.png)

像大多数其他教程一样检查 JavaScript 对象的存在(还有我)。

但有趣的是，在下一个搜索结果的[中，隐藏了 1 次点击。](https://www.mongodb.com/blog/post/optimizing-aws-lambda-performance-with-mongodb-atlas-and-nodejs)

[![](img/b0216c249523c79bcc61a2f31f82b818.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--g_4Zz5tE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AvotXj_W3tPr0zGpJlLbHLw.png)

我很惊讶这在任何地方都没有记载。

长话短说。我用`server.isConnected()`和**部署了代码，但**再也没有看到错误。

## 结论

[![](img/727064d77b077a4f7bd684e38beb8d88.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ctx2y4Pu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AF0AzCH6nfFx2re0FV3NFJw.png)

我希望我知道如何在 AWS Lambda 中处理 MongoDB 连接。

我希望直到今天。mongo 驱动的最新更新刚刚揭露了一个之前看不见的连续问题。