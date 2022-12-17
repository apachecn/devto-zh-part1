# 利用技术来加强你们的关系

> 原文：<https://dev.to/gyandeeps/use-technology-to-strengthen-your-relationship>

*此为原帖[此处](https://gyandeeps.com/technology-relationship/)T3】*

我于 2014 年 12 月结婚，结婚不到一个月，我妻子就不得不搬到内布拉斯加州的奥马哈，攻读她的硕士学位(两年制课程)。堪萨斯城只剩下我一个人(又是单身汉:)。因为我平时工作，周末做一些开源工作，所以我的生活非常忙碌。只有在晚上，我会和妻子聊上几分钟，然后上床睡觉。我妻子从来不满意我给她的时间，我甚至没有给她发短信。所以她最大的抱怨就是我白天跟她说的话不够多，连短信都不发。我知道这个星球上每个活着的人都从另一半那里听到过同样的话。

在经历了这一切之后，有一天我决定做点什么，作为一名软件工程师，我的第一个想法是为什么不使用技术和自动化这种类型的人类互动。那时我决定花几个小时在这个项目上。
现在我要转换一下思路，将上述情况转化为技术用户故事，解决手头的问题。

## 用户故事

*   每天给老婆发短信(我选择只发 1 条)。
*   确保信息措辞恰当，如“爱”、“想你”等。

## 设计

我想让这个过程自动化，这样我就可以继续做我正在做的事情，而我的妻子仍然可以收到消息。这样我也可以了解 AWS，因为每个人都在谈论它。

*   使用亚马逊网络服务
*   建立一个脚本，选择一个好的消息，并每天发送消息。
    *   我选择在 CDT 下午 4 点(随机时间)发送它

## 技术工作

使用这些亚马逊网络服务:

1.  CloudWatch <sup id="fnref1">[1](#fn1)</sup>
2.  λ<sup id="fnref2">T1】2T3】</sup>
3.  SNS<sup id="fnref3">T1】3T3】</sup>

### 云观察

设置云手表在一天中的特定时间运行我的 lambda 函数。这就像一个 cron 的工作。

*   用您选择的名称创建一个规则。
*   指定运行的时间间隔。
    *   使用的 Cron 表达式`0 21 * * ? *`(下午 4 点 CDT)
*   在目标中选择你的 lambda 函数。

### λ

这是整个项目的大脑。当这个函数被 CloudWatch 触发时，它基本上从消息数组中选取一条随机消息，然后将该消息发布到 SNS topic(稍后解释)。

*   用 hello world 模板创建一个 Lambda 函数
*   给它一些名称、描述，并使用 NodeJS 作为环境。
*   使用下面的代码，并填写评论中提到的所需信息

```
var AWS = require("aws-sdk");
var messages = [ // this where you want to put all the messages, I have like 100 messages here
    "hi",
    "bye"    
];

function getRandomMessage(){
    return messages[ Math.floor(Math.random() * messages.length ];
}

exports.handler = (event, context, callback) => {
    var sns = new AWS.SNS();
    var params = {
        Message: getRandomMessage(),
        Subject: "<Give a subject name (I just used my wife's name)>",
        TopicArn: "<use the topic arn number from SNS>"
    };
    sns.publish(params, context.done);
}; 
```

Enter fullscreen mode Exit fullscreen mode

*   创建一个角色，允许你的 lambda 函数在 SNS 主题 <sup id="fnref4">[4](#fn4)</sup> 上发表文章。

### SNS

我们将使用社交网络来设置一个话题，当你在这个话题上发帖时，它会向所有订阅者发送一条短信。

*   用您选择的任何名称创建一个主题
    *   你将得到你想在 lambda 函数代码中使用的主题 arn
*   在主题“创建订阅”中(除非您计划发送给许多人，否则您希望在这里创建订阅:p)
    *   将协议设置为 SMS
    *   设置单元格编号

## 结论

这对我帮助很大，因为我的妻子每天收到这些信息时都很兴奋。她知道是我在发送这些信息，但我总是否认。现在她看完这个帖子就知道了。

##### 参考文献

* * *

1.  [云观察](https://aws.amazon.com/cloudwatch/)T2】↩

2.  [λ](https://aws.amazon.com/lambda/)T2】↩

3.  [SNS](https://aws.amazon.com/sns/)T2】↩

4.  [AWS 权限模型](http://docs.aws.amazon.com/lambda/latest/dg/intro-permission-model.html) [↩](#fnref4)