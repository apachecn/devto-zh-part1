# 将您的 JavaScript 应用程序连接到 IFTTT

> 原文：<https://dev.to/johnmunsch/hooking-your-javascript-app-to-ifttt>

如果你从来没有用过“如果这个那么那个”( [IFTTT](https://ift.tt) )，那么你至少应该检查一下。它代表了一种将数百个能够触发事件的服务(“这个”)连接到数百个能够对事件做出反应的服务(“那个”)的简单方法。例如，你可以让[提前收到一封电子邮件](https://ifttt.com/applets/FQvrD6tS-snow-tomorrow-get-an-email-so-you-re-prepared)中的某种天气预报结果，或者[让《纽约时报》上新的热门文章直接进入你口袋里的链接](https://ifttt.com/applets/PrMB5L6V-automatically-save-new-popular-nytimes-books-articles-to-pocket)。

这些都是很好的组合，其中有成千上万的组合，人们通过无数种组合将网站、服务甚至物理硬件结合在一起。

但是也许你想让你自己最喜欢的应用程序连接到这个服务，这样你就可以让成百上千的事情发生。这实际上只是几分钟的工作，我将向您展示如何操作。

## 成为“本”

1.  首先，你必须去 [IFTTT](https://ift.tt) 如果你还没有帐户，注册一个。一个账户不需要任何费用。![Home Page](img/85b21ed21d9c4b046ac316a7da59037f.png)
2.  从右上方与您姓名相关的菜单中，选择“新建小程序”。那会把你带到这里。![Make an Applet](img/86fa4caba783ea22e623148e1980cda2.png)
3.  单击“this ”,这样我们可以选择一个将触发事件的服务，并键入“maker webhooks”以缩小选择范围。![Choose a Service for "This"](img/e4921fdf337a037eaf1ddb6c0e921e07.png)
4.  点击绿色图标进入下一页。![Maker Webhooks Service](img/b64050f2ebb1fbdfc3ba9736d593f117.png)
5.  Maker Webhooks 只有一个触发器，只需点击触发器，输入 IFTTT 需要的信息。![Choose Trigger](img/d585fbcc4bf35ff656d1185f2b0919f7.png)
6.  IFTTT 想要一段该事件特有的文本，它将成为您的 URL 的一部分。你不需要只有一个 Maker Webhooks 触发器，你可以有很多。这段文字将有助于区分它们。对于我的例子，我只使用了“测试事件”。![Receive a Web Request](img/650964f054cfb539d6cca024e7037df8.png)
7.  要查看下一页，您可能需要打开另一个标签，带有以下链接: [Maker Webhooks](https://ifttt.com/maker_webhooks) 。在右上角你会看到一个标有“文档”的按钮，你可以点击这里。您必须原谅我，因为我已经从您通常会在这一页上看到的三个地方剥离了唯一的密钥。当你查看你自己的页面时，你会看到一个很长的键(20 多个字符),它对你来说是独一无二的，出现在你得到的 URL 的末尾。每当我们想触发一个事件时，就会调用这个 URL。在 URL 中有一个标记为{ event }的点，这是我们之前输入的文本，用来告诉 IFTTT 我们正在触发哪个特定的事件。您还会注意到，您可以在调用的 JSON 主体中传递多条数据。根据 applet 中连接到该事件的服务，这些服务可能对另一方有用。![Maker Webhooks](img/a95c4e825bf8ac995ad9b58e3b4bf71c.png)
8.  忽略我们对 Maker Webhooks 文档的附带介绍，您会记得我们是在创建触发器。输入活动名称并点击“创建触发器”按钮后，您将回到这里。只需注意“this”部分现在已被填充。单击“that”链接完成此操作。![Finish the Applet](img/9b728a3e07eaa4ff32559a601cc0a4d6.png)
9.  现在是时候选择您想要连接到这个特定事件的服务了。与上面的一些页面不同，我截断了图像，所以它不是很长，我想完整地展示这一页，只是为了强调 JavaScript 触发器在别处执行某种操作的事件有多少选择。这是一个非常长的列表，非常值得花几分钟时间去探索。![Choose a Service for "That"](img/df8230f914d61dfbcb4e38a8d3cdc176.png)
10.  遗憾的是，对于这个例子，我将选择一些真正普通的东西。在字段中键入“电子邮件”以缩小选择范围，您会看到电子邮件和电子邮件摘要。我将使用第一个，但不要忽略第二个，这是一种可以触发多个事件的方式，但之后只会定期收到一封摘要电子邮件。本身就非常有用。![Email Services](img/ae5b5976bf13e16d2afae791209ea5ab.png)
11.  现在，您有机会为电子邮件提供一个主题以及电子邮件正文的格式。您可以在正文中使用 HTML 来格式化输出的内容，甚至可以提取通过 JSON 传入的值，以便嵌入到电子邮件输出中。*注意:我错误地认为 IFTTT 总是缩短我放入电子邮件正文的任何 URL，并通过 IFTTT 服务器重定向它们，但这似乎是一个你可以从设置页面关闭的选项。我希望以后能验证这一点。* ![Format the Subject and Body](img/7244537ace5820279eded233f7b681e1.png)
12.  在最后一步，你可以给你的 applet 取一个简短有力的名字来代替 IFTTT 软件提供的简单描述性名字。然后单击“完成”按钮创建并启用新的小程序。![Name Your Applet](img/c10cc0127234bcfceb5e6e0c47c1d8ad.png)

## 触发一个事件

如果您已经熟悉 Node.js，那么下一部分是非常基础的，但是如果您不熟悉，它可能是有用的。我举了一个简单的例子来说明如何调用它。这个例子没有使用任何类型的 NPM 包，只使用了基本的 Node.js 代码。

```
var http = require('http');
var url = require('url');

let key = '<insert your Maker Webhook key here>';

function triggerIftttMakerWebhook(event, key, value1, value2, value3) {
  let iftttNotificationUrl = `https://maker.ifttt.com/trigger/${event}/with/key/${key}`;
  let postData = JSON.stringify({ value1, value2, value3 });

  var parsedUrl = url.parse(iftttNotificationUrl);
  var post_options = {
      hostname: parsedUrl.hostname,
      port: parsedUrl.port,
      path: parsedUrl.path,
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Content-Length': postData.length
      }
  };

  // Set up the request
  var post_req = http.request(post_options, function(res) {
      res.setEncoding('utf8');
      res.on('data', function (chunk) {
        console.log('Response: ' + chunk);
      });
  });

  // Trigger a POST to the url with the body.
  post_req.write(postData);
  post_req.end();
}

triggerIftttMakerWebhook('test_event', key); 
```

## 多个小程序可以触发同一个事件

试试看，设置两个小程序，它们都为同一段文本触发，但执行不同的操作(可能是一封电子邮件和一条短信)。一个事件将触发两者。

## 成为“那个”

但所有这些都不是单向的。以上所有内容都集中在您的应用程序调用 IFTTT 作为触发器(“this”)，但是 Maker Webhooks 服务也可以用作 applet 中的“that”。因此，您可以在某个其他事件发生后获得一个 web 请求作为触发器。

在这种情况下，您可以指定要调用的 URL、方法(GET、POST、PUT、HEAD、DELETE 等中的任何一个。)，甚至将触发器中的一些值传递到主体中。这给了你的网络应用一些可视性，否则可能很难处理天气、股票交易、电子邮件、对亚马逊 Alexa 设备说的特定短语、Twitter 事件等。