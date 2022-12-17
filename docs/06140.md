# 地形化 AWS:无服务器网站后端，第 3 部分

> 原文：<https://dev.to/olivercole/terraforming-aws-a-serverless-website-backend-part-3>

这是我关于使用 Terraform 在 AWS 中构建无服务器后端的系列文章的第三部分。查看[第一部分](https://dev.toterraforming-aws-a-serverless-website-backend-part-1)开始吧。

## Mailgun

请记住，Terraform 支持各种各样的云提供商，您可以将它们混合在一起，以产生您想要的设计。

我们部署的代码使用了 Mailgun。

我们已经将 Mailgun API 键配置为一个 Terraform 变量——让我们为`terraform.tfvars` :
添加一个 SMTP 密码

```
mailgun_smtp_password = "hunter2" 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以在`terraform.tf`中设置好一切。

```
variable "mailgun_smtp_password" {}

provider "mailgun" {
  api_key = "${var.environment_configs["mail_api_key"]}"
}

resource "mailgun_domain" "serverless" {
  name          = "${var.environment_configs["mailgun_domain_name"]}"
  spam_action   = "disabled"
  smtp_password = "${var.mailgun_smtp_password}"
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个接触参数和属性的好时机——你配置的所有值，比如`spam_action = "disabled"`，都是**参数**——就像在其他语言中调用构造函数或函数一样。
**属性**是资源上一旦创建就存在的值——例如分配的 IP 地址或 URL，就像其他语言中的实例属性一样。我们之前在创建`aws_kms_alias`时使用了它，例如:

```
 target_key_id = "${aws_kms_key.LambdaBackend_config.key_id}" 
```

Enter fullscreen mode Exit fullscreen mode

`mailgun_domain`资源为您需要创建的 DNS 记录提供了两个**属性**，以便 Mailgun 为您的域发送和接收电子邮件。有两种方法可以得到这个设置。

#### 手动设置邮箱 DNS 记录

第一种方法是简单地从 Terraform 输出 DNS 记录，并在您的 DNS 提供商手动应用它们。

当我们为 Lambda 函数设置 SSM 参数存储条目时，我们使用了变量——特别是从我们的`terraform.tfvars`文件传递到`ssm_parameter_map.tf`的输入变量。Terraform 还支持[输出变量](https://www.terraform.io/intro/getting-started/outputs.html)——要么来自`ssm_parameter_map`这样的模块，要么来自我们的‘根’`terraform.tf`模块。

添加这些输出变量，从部署的`mailgun_domain` :
中输出 DNS 记录

```
output "send" {
  value = "${mailgun_domain.serverless.sending_records}"
}

output "receive" {
  value = "${mailgun_domain.serverless.receiving_records}"
} 
```

Enter fullscreen mode Exit fullscreen mode

然后只需`terraform apply`，所需的 DNS 记录将在运行结束时显示。(如果这不起作用，你可能忘记做什么了？)

#### 使用 Terraform 设置 Mailgun DNS 记录

或者，此时您可能已经了解了足够多的 Terraform，可以尝试自动设置 DNS 记录了！Terraform 支持多种 DNS 提供商，包括 [AWS](https://www.terraform.io/docs/providers/aws/r/route53_record.html) (你可以考虑使用[这个模块](https://github.com/samstav/tf_mailgun_aws))、 [DNSMadeEasy](https://www.terraform.io/docs/providers/dme/index.html) 、 [DNSimple](https://www.terraform.io/docs/providers/dnsimple/index.html) 、 [Cloudflare](https://www.terraform.io/docs/providers/cloudflare/index.html) 、 [Dyn](https://www.terraform.io/docs/providers/dyn/index.html) 和 generic [RFC 2136 access](https://www.terraform.io/docs/providers/dns/index.html) 。

如果你使用的不是域名注册商的默认设置，AWS 对我个人来说已经很好了，但是需要一点费用。

利用上面提到的`sending_records`和`receiving_records`属性来设置您的提供商，并确保您在继续之前已经运行了`terraform apply`。如果您需要处理记录列表或调整任何字符串的格式，请查看[插值文档](https://www.terraform.io/docs/configuration/interpolation.html)了解详情。

# 测试λ

您可以通过进入 Lambda 控制台，使用测试事件:
来测试您的 Lambda 函数

```
{ "body-json": "email=user%40domain.com"} 
```

Enter fullscreen mode Exit fullscreen mode

或者，您可以在命令行上执行此操作:

```
aws lambda invoke --function-name LambdaBackend --payload "{ \"body-json\": \"email=user%40domain.com\"}" out.txt 
```

Enter fullscreen mode Exit fullscreen mode

无论哪种方式，您都应该在该地址收到一封电子邮件，结果应该是这样的:

```
{
  "errorMessage": "Email.MovedPermanently : Redirecting.",
  "errorType": "http://your/redirect/URL",
  "stackTrace": [
    "redirect (/var/task/index.js:50:19)",
    "/var/task/index.js:111:20",
    "transporter.send (/var/task/node_modules/nodemailer/lib/mailer/index.js:187:21)",
    "/var/task/node_modules/nodemailer-mailgun-transport/src/mailgun-transport.js:149:9",
    "Request.finalCb [as callback] (/var/task/node_modules/nodemailer-mailgun-transport/node_modules/mailgun-js/lib/request.js:99:12)",
    "IncomingMessage.<anonymous> (/var/task/node_modules/nodemailer-mailgun-transport/node_modules/mailgun-js/lib/request.js:313:17)",
    "emitNone (events.js:91:20)",
    "IncomingMessage.emit (events.js:185:7)",
    "endReadableNT (_stream_readable.js:974:12)",
    "_combinedTickCallback (internal/process/next_tick.js:80:11)",
    "process._tickDomainCallback (internal/process/next_tick.js:128:9)"
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

Lambda 函数返回一个错误，其原因您将在后面看到。

如果你的函数不工作，看看你的函数的 Cloudwatch 日志。

# API 网关

现在这个函数已经在 AWS 中工作了，我们需要将它连接到 API 网关，以向外界公开它。

本节从 API 网关设置日志:

```
resource "aws_api_gateway_account" "gateway" {
  cloudwatch_role_arn = "${aws_iam_role.cloudwatchlog.arn}"
}

resource "aws_iam_role" "cloudwatchlog" {
  name = "cloudwatchlog"

  assume_role_policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": {
        "Service": "apigateway.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOF
}

resource "aws_iam_policy_attachment" "cloudwatchlog" {
  name       = "cloudwatchlog"
  roles      = ["${aws_iam_role.cloudwatchlog.name}"]
  policy_arn = "arn:aws:iam::aws:policy/service-role/AmazonAPIGatewayPushToCloudWatchLogs"
} 
```

Enter fullscreen mode Exit fullscreen mode

本节设置实际的 API、资源和集成。如果你想走那条路，也有 Terraform 模块可供选择。

```
resource "aws_api_gateway_rest_api" "service" {
  name = "BackendService"
}

resource "aws_api_gateway_resource" "api" {
  rest_api_id = "${aws_api_gateway_rest_api.service.id}"
  parent_id   = "${aws_api_gateway_rest_api.service.root_resource_id}"
  path_part   = "api"
}

resource "aws_api_gateway_resource" "email" {
  rest_api_id = "${aws_api_gateway_rest_api.service.id}"
  parent_id   = "${aws_api_gateway_resource.api.id}"
  path_part   = "email"
}

resource "aws_api_gateway_method" "post" {
  rest_api_id   = "${aws_api_gateway_rest_api.service.id}"
  resource_id   = "${aws_api_gateway_resource.email.id}"
  http_method   = "POST"
  authorization = "NONE"
}

resource "aws_api_gateway_integration" "integration" {
  rest_api_id             = "${aws_api_gateway_rest_api.service.id}"
  resource_id             = "${aws_api_gateway_resource.email.id}"
  http_method             = "${aws_api_gateway_method.post.http_method}"
  integration_http_method = "POST"
  type                    = "AWS"
  uri                     = "arn:aws:apigateway:${var.region}:lambda:path/2015-03-31/functions/${aws_lambda_function.LambdaBackend_lambda.arn}:$${stageVariables.alias}/invocations"
  passthrough_behavior    = "WHEN_NO_TEMPLATES"

  request_templates {
    "application/x-www-form-urlencoded" = <<EOF
##  See http://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-mapping-template-reference.html
##  This template will pass through all parameters including path, querystring, header, stage variables, and context through to the integration endpoint via the body/payload
#set($allParams = $input.params())
{
"body-json" : $input.json('$'),
"params" : {
#foreach($type in $allParams.keySet())
    #set($params = $allParams.get($type))
"$type" : {
    #foreach($paramName in $params.keySet())
    "$paramName" : "$util.escapeJavaScript($params.get($paramName))"
        #if($foreach.hasNext),#end
    #end
}
    #if($foreach.hasNext),#end
#end
},
"stage-variables" : {
#foreach($key in $stageVariables.keySet())
"$key" : "$util.escapeJavaScript($stageVariables.get($key))"
    #if($foreach.hasNext),#end
#end
},
"context" : {
    "account-id" : "$context.identity.accountId",
    "api-id" : "$context.apiId",
    "api-key" : "$context.identity.apiKey",
    "authorizer-principal-id" : "$context.authorizer.principalId",
    "caller" : "$context.identity.caller",
    "cognito-authentication-provider" : "$context.identity.cognitoAuthenticationProvider",
    "cognito-authentication-type" : "$context.identity.cognitoAuthenticationType",
    "cognito-identity-id" : "$context.identity.cognitoIdentityId",
    "cognito-identity-pool-id" : "$context.identity.cognitoIdentityPoolId",
    "http-method" : "$context.httpMethod",
    "stage" : "$context.stage",
    "source-ip" : "$context.identity.sourceIp",
    "user" : "$context.identity.user",
    "user-agent" : "$context.identity.userAgent",
    "user-arn" : "$context.identity.userArn",
    "request-id" : "$context.requestId",
    "resource-id" : "$context.resourceId",
    "resource-path" : "$context.resourcePath"
    }
}

EOF
  }
}

resource "aws_api_gateway_method_response" "301" {
  rest_api_id = "${aws_api_gateway_rest_api.service.id}"
  resource_id = "${aws_api_gateway_resource.email.id}"
  http_method = "${aws_api_gateway_method.post.http_method}"
  status_code = "301"
  depends_on  = ["aws_api_gateway_integration.integration"]

  response_parameters = {
    "method.response.header.Location" = true
  }
}

resource "aws_api_gateway_integration_response" "default" {
  rest_api_id       = "${aws_api_gateway_rest_api.service.id}"
  resource_id       = "${aws_api_gateway_resource.email.id}"
  http_method       = "${aws_api_gateway_method.post.http_method}"
  status_code       = "${aws_api_gateway_method_response.301.status_code}"
  selection_pattern = "^Email.MovedPermanently.*"

  response_parameters = {
    "method.response.header.Location" = "integration.response.body.errorType"
  }

  depends_on = ["aws_api_gateway_integration.integration"]
}

resource "aws_api_gateway_deployment" "deploy" {
  depends_on = ["aws_api_gateway_method.post", "aws_api_gateway_integration.integration", "aws_api_gateway_integration_response.default"]

  rest_api_id = "${aws_api_gateway_rest_api.service.id}"
  stage_name  = "${terraform.env}"

  variables = {
    "alias" = "${terraform.env}"
  }
}

resource "aws_api_gateway_method_settings" "settings" {
  rest_api_id = "${aws_api_gateway_rest_api.service.id}"
  stage_name  = "${aws_api_gateway_deployment.deploy.stage_name}"
  method_path = "${aws_api_gateway_resource.api.path_part}/${aws_api_gateway_resource.email.path_part}/${aws_api_gateway_method.post.http_method}"

  settings {
    metrics_enabled = true
    logging_level   = "INFO"
  }
}

resource "aws_lambda_alias" "alias" {
  name             = "${terraform.env}"
  function_name    = "${aws_lambda_function.LambdaBackend_lambda.arn}"
  function_version = "$LATEST"
}

resource "aws_lambda_permission" "invoke" {
  statement_id  = "${terraform.env}Invoke"
  action        = "lambda:InvokeFunction"
  function_name = "${aws_lambda_function.LambdaBackend_lambda.arn}"
  principal     = "apigateway.amazonaws.com"
  source_arn    = "arn:aws:execute-api:${var.region}:${data.aws_caller_identity.current.account_id}:${aws_api_gateway_rest_api.service.id}/*/${aws_api_gateway_method.post.http_method}/${aws_api_gateway_resource.api.path_part}/${aws_api_gateway_resource.email.path_part}"
  qualifier     = "${terraform.env}"
  depends_on    = ["aws_lambda_alias.alias"]
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们为 Lambda 函数设置 SSM 参数存储条目时，我们使用了变量——特别是从我们的`terraform.tfvars`文件传递到`ssm_parameter_map.tf`的输入变量。Terraform 还支持[输出变量](https://www.terraform.io/intro/getting-started/outputs.html)——要么来自`ssm_parameter_map`这样的模块，要么来自我们的‘根’`terraform.tf`模块。

添加这个输出变量来输出来自部署的 API 网关的路径:

```
output "invoke_url" {
  value = "${aws_api_gateway_deployment.deploy.invoke_url}/${aws_api_gateway_resource.api.path_part}/${aws_api_gateway_resource.email.path_part}"
} 
```

Enter fullscreen mode Exit fullscreen mode

当你`terraform apply`你将得到变量的值，打印在输出的末尾:

```
Outputs:

invoke_url = https://api_id_here.execute-api.eu-west-1.amazonaws.com/default/api/email 
```

Enter fullscreen mode Exit fullscreen mode

# 测试 API

现在你的 Lambda 函数被公开为一个 API。

1.  启动 Postman，输入 invoke_url 中的值作为您的 url。
2.  将方法更改为`POST`
3.  在 Body 选项卡上，将类型设置为`x-www-form-urlencoded`
4.  添加关键字`email`和目标电子邮件地址作为值。
5.  点击发送！

邮递员应该按照重定向到您配置的 URL，您应该在您的收件箱中收到电子邮件。恭喜你！

# 混乱和毁灭

如果您想在 AWS 和 Mailgun 上拆除这些服务的配置，您可以简单地运行`terraform destroy`并在询问时确认。您的`terraform.tf`文件将不受影响。

例如，DynamoDB 提供的吞吐量确实会产生运行成本，所以您可能希望在完成本系列文章后考虑这样做。

# 下一步

Rob 的[原始文章](http://robsherling.com/jbytes/index.php/2017/01/08/serverless-backends-with-aws-cloud-intro/)继续添加更多的功能，并利用额外的 AWS 服务。然而，这样做可能不需要了解更多关于 Terraform 的知识，所以我将暂停这个系列文章，收集一些反馈。如果有足够多的人觉得这有用，我会完成这个系列。

如果您有任何问题或意见，请在评论中告诉我，尤其是如果您觉得这很有用的话！