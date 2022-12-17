# 日常生活中的黑客(Sprint API 带来的乐趣)

> 原文：<https://dev.to/anotherdevblog/hacking-in-everyday-life-fun-with-the-sprint-api>

让我解释一下我上周的处境:

如果你还不知道的话，Sprint 正在提供一年免费的无限制服务，如果你把你的非 Sprint 号码移植给他们，服务将持续到 7 月底。这很划算，但如果你不是威瑞森的客户，他们真的会让你为此而努力。如果你使用的是较小的运营商，免费年页面可能会说你不符合条件，即使你有符合条件的设备类型。这是因为 Sprint 维护着自己的手机数据库，每个记录上都有一些指示符，表明该手机是否在 Sprint 网络上(你没听错:这是一个不在*和*网络上的手机数据库)。因此，如果你的运营商(比如我的运营商)没有告诉 Sprint 你的手机不在 Sprint 网络上，Sprint 系统会认为你没有资格享受这项服务。

解决方案是使用 Sprint 创建一个支持案例，将您的设备信息(WiFi MAC 地址、IMEI 号码、电话号码、品牌/型号和当前运营商)添加到数据库中。从尝试过这种方法的家庭成员的经验来看，我知道将信息处理到数据库并反映在免费年页面上可能需要三天时间，并且我可能需要更长时间才能收到来自 Sprint 的电子邮件，说一切都很好。但是时间是最重要的，所以我必须每隔几个小时就查看一下免费的一年页面，看看我想移植的每一个设备什么时候有优惠。真麻烦！

这就是开发人员派上用场的地方。

我启动了我可信赖的[Postman Interceptor](https://chrome.google.com/webstore/detail/postman-interceptor/aicmkgpgakddgnaphhhpliifpcfhicfo)Chrome 扩展，并对我仍然不合格的手机发起了一个查询。在流量历史中，我看到一个 AJAX HTTP 请求发送到一个看起来很有希望的 sprint.com 端点，它返回了一些看起来很有希望的 JSON:

```
{  "errors":  [  {  "errorCode":  "COULD_NOT_VALIDATE_SERIAL_NUMBER",  "errorMessage":  "Server.704:NMS returned status_code=49, status_text=INVALID_PHONE_OWNERSHIP: Phone owner can not be PLBL."  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

这个请求是一个 GET，但是非常复杂:各种各样的消息 id、令牌和时间戳。幸运的是，Postman 使修改和重放它变得容易。因此，我开始剥离标题，看看哪些我可以牺牲。幸运的是，我的浏览器发送的大部分头被证明是不必要的，而剩下的必需的头，没有一个被真正验证了准确性 Sprint API 只需要在那些字段中有一些值。最后，我可以将整个 HTTP 请求精简为这样(其中 xxxxxxxxxxxx 表示我想要查询的设备的 IMEI):

```
GET /api/digital/byod/v1/foreign-device/xxxxxxxxxxxx HTTP/1.1
Host: www.sprint.com
messageid: 1000
enterprisemessageid: ECMW593313374
messagedatetimestamp: 2017-07-19T22:56:59
applicationuserid: LVO
applicationid: LVO 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。但是每隔几个小时就必须手动完成这项工作，这仍然很繁琐。是时候实现自动化了。我本可以为此在我的机器上设置一个 chron 作业，但这实际上是一个引入我的好朋友 [Google Apps 脚本](https://www.google.com/script/start/)的绝佳机会。我创建了一个新的脚本，编写了下面这个简单的函数，并设置了一个触发器，让谷歌每 15 分钟运行一次:

```
function run() {
  if (isReady('xxxxxxxxxxxx')) {
    GmailApp.sendEmail('myemail@mydomain.com', 'YOUR PHONE IS IN THE DB NOW!', 'Go sign up for your free year');
    // Delete the 15-minute trigger I manually created earlier so I don't get spammed
    ScriptApp.deleteTrigger(ScriptApp.getProjectTriggers()[0]);
  } else {
    Logger.log('The phone is NOT in the DB'); 
  }
}

function isReady(imei) {
  Logger.log('Calling the Sprint API');
  var responseText = UrlFetchApp.fetch('https://www.sprint.com/api/digital/byod/v1/foreign-device/' + imei, {
    'method': 'get',
    'headers': {
      'messageid': '1000',
      'enterprisemessageid': 'ECMW593313374',
      'messagedatetimestamp': '2017-07-19T22:56:59',
      'applicationuserid': 'LVO',
      'applicationid': 'LVO'
    },
    'muteHttpExceptions': true
  }).getContentText();

  // Log the response from the API
  Logger.log(responseText);

  // Parse it and return whether the object exists and has no errors
  var response = JSON.parse(responseText);
  return response && !response.errors;
} 
```

Enter fullscreen mode Exit fullscreen mode

第二天，我收到了一封来自我的脚本的电子邮件，通知我现在可以扑向免费年计划。我想我一定是程序出了问题，所以我检查了日志。这是最后一次记录的 API 响应，愉快地确认我现在确实有资格享受 365 天的无限制 LTE:

```
{  "byodDetails":  {  "serialNo":  "xxxxxxxxxxxx",  "deviceSKU":  "yyyyyyyyyyyyy",  "netCompatibility":  null,  "iccId":  null,  "simKitInfo":  [  {  "simSKU":  "zzzzzzzzzzzzzzz",  "simPartNumber":  "aaaaaaaaaaaa",  "simPriority":  "1",  "simSKUPricingInfo":  {  "finalPrice":  2.99,  "basePrice":  2.99  }  }  ]  }  } 
```

Enter fullscreen mode Exit fullscreen mode

我想说的是，设置 Postman、处理请求和编写我的 Google Apps 脚本总共花了一个小时。每天花一个小时来自动完成对我家中每台设备进行手动验证的繁琐、耗时数天的过程。

这让我想知道:我还能黑什么？还有哪些 API？我还能在哪里写一点代码，把我几个小时的生活找回来呢？

你的生活中有什么是*你*可以自动化的？

* * *

这篇文章最初出现在[另一个开发者博客](https://goo.gl/6tM2x8)