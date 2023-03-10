# 减轻无服务器 AWS 的痛苦——如何从 AWS API 返回 HTML

> 原文：<https://dev.to/luispa/ease-the-pain-of-serverless-aws--how-to-return-html-from-aws-api-gateway--lambda>

之前发布于此处的[。](https://kennbrodhagen.net/2016/01/31/how-to-return-html-from-aws-api-gateway-lambda/)

# 为什么从 API 网关返回 HTML？

我最近和一个客户一起工作，他需要为社交媒体创建定制的 html 元标签。他有一个 AngularJS 单页应用程序，我们需要一种快速的方法来为脸书、Twitter 和 Google Plus 创建好看的社交媒体卡片。由于该应用程序基于 Angular，我们没有可以轻松利用的服务器端基础设施。我们决定创建一个 API 网关和 Lambda 函数来满足我们的需求。开箱即用的 API Gateway 确实希望使用 application/json 内容，因此它需要稍加调整才能使用输出 HTML 的 Lambda 函数。

# 概观

在这个示例中，您将首先创建一个 Lambda 占位符函数，该函数返回 HTML 并将其绑定到 API Gateway 中的一个 API。占位符函数将返回一个硬编码的 HTML 字符串。一旦有了框架，就可以用一个更复杂的版本来替换这个函数。

# Lambda 占位符

创建一个简单的 Lambda 函数，返回一个 HTML 字符串。

1.  登录到 AWS 控制台并导航到 Lambda 服务。
2.  创建一个新的 Lambda 函数并选择 hello-world 模板。
3.  将函数命名为 lambda-html。
4.  输入下面的代码块来返回一些基本的 html。
5.  选择创建一个新的*Basic 执行角色，或者如果您之前已经创建了 Lambda，您应该会在列表底部看到 lambda_basic_execution。
6.  保留默认的高级设置。
7.  保存您的函数并继续运行测试。你给它什么输入并不重要。

```
// Lambda Placeholder
console.log('Loading Lambda HTML');

exports.handler = function(event, context) {
    var html = '<html><head>HTML from API Gateway/Lambda</head>' + 
        '<body><h1>HTML from API Gateway/Lambda</h1></body></html>';

    context.succeed(html);
}; 
```

Lambda 函数返回 HTML
[![](img/c2d5e52dd2f1a8ebdd5e43c9128773eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gjU0zsn6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/htbpz9mshfmjevd2tprc.jpg)

Lambda 函数返回 HTML
[![](img/6c9e8eb2ee7be8d591ab24e2138c7fdf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hXOPme9D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uphosxexe2ro0y1190ae.jpg)

# API 网关

现在转到 api 网关服务来创建我们的 API，并将其绑定到 Lambda 函数。

# 创建新的 API

1.  导航到 API 网关服务并创建一个名为 `html-response-api`的新 API。
2.  在根资源上创建一个 GET 方法，并将其绑定到您的 Lambda `lambda-html.`

创造获取方法
[![](img/87acbde89a53f2e4090808eeb00c72b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KJtvPPd8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/of0pwfnaughjemfrm1ny.jpg)

# 方法响应

1.  导航到 API 的 GET 方法的方法响应。
2.  打开 HTTP 状态下的 200，添加一个名为 `Content-Type` 的响应头。一定要用绿色复选标记保存它。如果您尝试按 enter 键保存，将会清除该字段，而不是保存。
3.  删除 200 的应用程序/json 响应模型。
Add the Content-Type header and clear the Response Models

![](img/50751e42d3e399dfdb850d28fcaf433f.png)

# 整合响应

1.  导航到 API 的 GET 方法的集成响应。
2.  打开 200 响应、标题映射和映射模板。
3.  编辑响应头内容类型，并将映射值设置为`'text/html'`(确保使用单引号)。不要忘记用绿色勾号图标保存它。
4.  删除映射模板下的应用程序/json 内容类型。
5.  添加内容类型`'text/html'`(这次没有引用)。
6.  在右边的下拉框中选择***映射模板* * *。
7.  将模板的值设置为`input.path('$')`并用绿色勾号保存。

将 Content-Type 改为 text/html，并将 Lambda 的响应字符串映射为整个输出。
[![](img/542c5e2a4a1639195445b1f0954bda5a.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--yTcQoQp4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mjwijtinhjq4yehvnknx.jpg)

# 部署和测试 API

1.  单击蓝色的部署 API 按钮。
2.  说出舞台棒或任何你喜欢的东西。
3.  单击 URL 打开新的浏览器选项卡并查看结果。

将 API 部署到生产阶段。
[![](img/2bb22e2f40fcfee2d5c774ea53f2d0c4.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--6KXorxbM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9kkoy3oeqo3w05kusmfz.jpg)

您的来自 Lambda 的 HTML 成功呈现。
[![](img/fda12c802e1932cab0d9fbfdf02d7c1d.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--G8r_5J-7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wuzyxgnqkntnvfrqlvlb.jpg)

# 更进一步

现在，您可以定制 Lambda 函数来执行您自己的业务逻辑。以下是一些想法:

*   从数据库或 [mockapi](http:/mockapi.io) 获取结果。
*   从模板中呈现你的 HTML。
*   向 API 请求添加一个 URL 或查询参数，并将其传递给 Lambda 函数。如果您需要帮助，请参阅我的关于向 Lambda 传递 URL 和查询参数的文章。(视频:[https://youtu.be/2Z-Utw_xl4c](https://youtu.be/2Z-Utw_xl4c)17m in)

我希望你喜欢它。继续拍打键盘，直到有好事发生。