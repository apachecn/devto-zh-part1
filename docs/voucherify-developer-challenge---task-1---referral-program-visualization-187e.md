# 奖励开发者挑战-任务 1 -推荐计划可视化

> 原文：<https://dev.to/msedzielewski/voucherify-developer-challenge---task-1---referral-program-visualization-187e>

## [第一届凭证开发者挑战赛](https://www.voucherify.io/blog/voucherify-developer-challenge?utm_campaign=contest1&utm_medium=post&utm_source=dev.to)。阅读下面的任务，开始破解吧！您有 3 周时间提交您的解决方案。

见见玛丽。她是一名开源贡献者，也是另一个 JS Framework 的创始人。Mary 和她的团队为开源项目注册了一个免费的 Voucherify 帐户，最近还开展了一项推荐活动来推广他们的赃物商店——这是捐赠按钮的一种替代方式。玛丽给出了唯一的推荐代码。

玛丽的程序是如何工作的？想象你是推荐人之一。你会得到一个独一无二的代码，你可以把它贴满整个互联网。如果一个新的购物者用你的代码买东西，他们会得到 10%的折扣。推荐计划有多个层级，因此您的奖励取决于您吸引了多少客户:

1.  如果你吸引了一个人，你可以得到 5 美元的折扣
2.  当数量超过 5 时，您可以享受 10 美元的折扣
3.  当数量超过 10 时，您将获得 25 美元的礼品卡

推荐活动推出几周后，她想了解一下推荐人的表现，以及哪些推广渠道在这一领域做得更好。Mary 要求您分析她的凭证数据，并将结果可视化。

任务:
根据推荐历史，想象 Mary 的活动表现。

玛丽可能感兴趣的线索:

*   谁每周吸引了最多的购物者，吸引了最多的购物者
*   谁创造了最多的钱
*   哪种营销渠道最有效
*   推荐图从一开始是如何发展的？
*   等等。

其他要求:

*   该报告应该是一个动态网站的形式，这意味着它应该显示来自 Voucherify 的最新信息
*   没有技术栈限制，可以是一个静态的 JS 页面，可以在服务器端呈现。你可以使用 React，Angular，Ember，jQuery，d3，甚至 GWT。

如何开始:

*   熟悉 Voucherify API 参考中的[兑换对象](https://docs.voucherify.io/reference#the-redemption-object)。这就是优惠券“用法”的存储方式。
*   为您找到两个重要的字段:
    *   凭证代码-客户使用的实际代码
    *   订单.金额-他花的钱
*   可以使用[列表兑换点](https://docs.voucherify.io/reference#list-redemptions)检索兑换(API 键如下)。您应该只加载推荐活动中的内容
*   Mary 还提供了从客户数据库导出的 CSV 文件。它包括以下各列:
    *   推荐代码(可以链接到兑换对象中的凭证代码)
    *   名字
    *   链接到照片
    *   获得客户的渠道
*   浏览我们的[SDK](https://docs.voucherify.io/docs/sdks)，选择一个并开始做吧！

如何将赎回对象链接到客户记录的示例。在下面的场景中，客户*cust _ yd dou 0 hhx 3 vmunddmy 9 y0 ztl*(名为祝福梅杰)兑换了 *yajsf-o4pX6u8g* ，这是他从*gokhan pektemek*处收到的转介码。他花了 50 美元买了些东西。

兑换对象:

```
{  "id":"r_f393fFIFDWxO1koOGPR6lcQY",  "object":"redemption",  "date":"2016-12-20T19:00:00.000Z",  "customer_id":"cust_yDdoU0Hhx3VmUNDdmy9Y0Ztl",  "tracking_id":null,  "order":  {  "object":"order",  "source_id":null,  "amount":5000,  "status":"PAID",  "metadata":null  },  "metadata":null,  "result":"SUCCESS",  "customer":{  "object":"customer",  "id":"cust_yDdoU0Hhx3VmUNDdmy9Y0Ztl",  "name":"blessing meijer",  "source_id":null  },  "related_object_type":"voucher",  "voucher":{  "object":"voucher",  "code":"yajsf-o4pX6u8g",  "campaign":"referral"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

客户 CSV 导出文件格式:

`yajsf-o4pX6u8g,gökhan pektemek,https://randomuser.me/api/portraits/thumb/men/72.jpg,hackernews`

‍
**截止日期**:2018 年 2 月 11 日 23:59 utc/2018 年 2 月 11 日晚上 11:59 utc
**如何提交**:将现场演示链接和源代码(您仍然是代码的所有者)发送到 mike (at) voucherify.io，在您的博客或 readme.md 文件中解释解决方案可获得加分。
在哪里免费举办试玩: [Heroku](https://www.heroku.com/) 、 [OpenShift](https://www.openshift.com/) 、Glitch(仅限 Node.js)、 [Netlify](https://www.netlify.com/) (仅限静态网站)
**奖品** :
第一名——一个可黑客(阅读 Kenneth 的 [config](https://blog.lessonslearned.org/building-a-more-secure-development-chromebook/) ) [三星 chrome book 3 11.6](https://www.samsung.com/us/computing/chromebooks/under-12/chromebook-3-11-6---32gb-hdd--4gb-ram--xe500c13-k03us/)
[![](img/c63db8d2dbbe41aaaf887e736c1fbc60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QEScVDQb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daks2k3a4ib2z.cloudfront.net/58fe5d0657dd045f17ae234e/5a65ceb9b0439c000139f619_chromebook.png)

社区奖(我们将让您投票选出 dev.to 主题中的最佳贡献！)-一件时尚的 **dev.to T 恤**(非常感谢 dev.to 团队！)、**赠 t 恤**，还有一件**骤回**
[![](img/7bf5ec16ff58debb7fcb46970c47d0a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ftxl4Lv3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daks2k3a4ib2z.cloudfront.net/58fe5d0657dd045f17ae234e/5a65cf356b264200019bdf1f_tshirts.jpg)

资源:

*   你不必创建一个凭证帐户，这里是 API 的密钥(只读):
    *   申请钥匙:*dfe 8716 e-D5 C4-4c6d-a215-609 add 103 fcf*
    *   安全令牌:*d2d 16589-79ec-46f 7-b921-FBA 09 a9 a9 baa*
*   [列出赎回参考](https://docs.voucherify.io/reference#list-redemptions)
*   [从客户数据库导出 CSV 文件](https://drive.google.com/open?id=1AkE70sYk-dKTjtxsHvbG_DVH7x8iKhei)
*   [论坛](https://github.com/voucherifyio/developer-challenge)
*   [条款](https://docs.google.com/document/d/1gneCfQdg6ZVTt_jR-w6aLURBDo2OTrCF7wlW_W_P_tI/edit?usp=sharing)