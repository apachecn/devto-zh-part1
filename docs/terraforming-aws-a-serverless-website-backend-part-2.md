# 地形化 AWS:无服务器网站后端，第 2 部分

> 原文：<https://dev.to/olivercole/terraforming-aws-a-serverless-website-backend-part-2>

这是我关于使用 Terraform 在 AWS 中构建无服务器后端的系列文章的第二部分。查看[第一部分](https://dev.toterraforming-aws-a-serverless-website-backend-part-1)开始吧。

## 数据来源和加密

接下来是一个新概念:数据源。这些允许您在运行时从其他外部来源获取数据。在第一部分的末尾，我们添加了这个:

```
data "aws_caller_identity" "current" {} 
```

Enter fullscreen mode Exit fullscreen mode

[`aws_caller_identity`](https://www.terraform.io/docs/providers/aws/d/caller_identity.html) 允许您获取帐户的 AWS 用户 ID，并用于构建策略文档和 ARNs，使用与之前完全相同的插值:

```
resource "aws_kms_key" "LambdaBackend_config" {
  description             = "LambdaBackend_config_key"
  deletion_window_in_days = 7

  policy = <<POLICY
{
  "Version" : "2012-10-17",
  "Id" : "key-consolepolicy-3",
  "Statement" : [ {
    "Sid" : "Enable IAM User Permissions",
    "Effect" : "Allow",
    "Principal" : {
      "AWS" : "arn:aws:iam::${data.aws_caller_identity.current.account_id}:root"
    },
    "Action" : "kms:*",
    "Resource" : "*"
  }, {
    "Sid" : "Allow use of the key",
    "Effect" : "Allow",
    "Principal" : {
      "AWS" : "${aws_iam_role.LambdaBackend_master_lambda.arn}"
    },
    "Action" : [ "kms:Encrypt", "kms:Decrypt", "kms:ReEncrypt*", "kms:GenerateDataKey*", "kms:DescribeKey" ],
    "Resource" : "*"
  }, {
    "Sid" : "Allow attachment of persistent resources",
    "Effect" : "Allow",
    "Principal" : {
      "AWS" : "${aws_iam_role.LambdaBackend_master_lambda.arn}"
    },
    "Action" : [ "kms:CreateGrant", "kms:ListGrants", "kms:RevokeGrant" ],
    "Resource" : "*",
    "Condition" : {
      "Bool" : {
        "kms:GrantIsForAWSResource" : "true"
      }
    }
  } ]
}
POLICY
}

resource "aws_kms_alias" "LambdaBackend_config_alias" {
  name          = "alias/LambdaBackend_config"
  target_key_id = "${aws_kms_key.LambdaBackend_config.key_id}"
} 
```

Enter fullscreen mode Exit fullscreen mode

当我们为服务创建一些配置时，我们将加密它——这些资源允许我们这样做！

这些你都可以做到。

Terraform 是 1.0 版之前的软件，`aws_kms_key`资源有时会抛出错误。如果出现这种情况，就再`terraform apply`一次，并关注[这一期！](https://github.com/terraform-providers/terraform-provider-aws/issues/245)

# 应用配置

现在我们要为我们的 Lambda 函数定义一些配置。将应用程序代码和配置分开是一个很好的实践，更好的实践是不要将后者签入源代码控制。我们将使用 AWS EC2 系统管理器(称为 SSM)存储我们的配置，并在此过程中了解 Terraform 变量和模块。

我们将把我们的配置存储在 SSM [参数存储库](https://aws.amazon.com/ec2/systems-manager/parameter-store/)中。这是一个键值存储，对于简单的配置字符串、API 键和密码非常有用——而且是免费的！您可以将内容配置为使用我们刚刚创建的密钥进行加密，并使用我们创建的 IAM 策略控制对内容的访问。

Terraform 中的[变量](https://www.terraform.io/language/values/variables)与大多数编程语言的工作方式相似。您在代码中声明它们，可能带有一个值，然后使用`${var.variable_name}`语法检索该值，类似于上面的数据源。您还可以在命令行上或在单独的文件中指定这些变量值，这意味着您不必将您的秘密签入源代码控制。更多信息见[如何使用地形变量](https://spacelift.io/blog/how-to-use-terraform-variables)。

Terraform 地图类似于其他语言中的哈希表、哈希表、字典或类似的数据结构。继续在`terraform.tf`中定义它。

```
variable "environment_configs" {
  type = "map"
} 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以在与`terraform.tf` :
相同的目录下创建一个名为`terraform.tfvars`的新文件

```
environment_configs = {
  site_callback = "CALLBACK TO RETURN TO FROM LAMBDA",
  email_table_name = "EMAIL DYNAMODB TABLE",
  aes_password = "AES PASSWORD",
  mail_api_key = "MAILGUN KEY",
  mailgun_domain_name = "MAILGUN DOMAIN",
  from_email = "OUTGOING EMAIL ACCOUNT"
} 
```

Enter fullscreen mode Exit fullscreen mode

关于变量内容的一些提示:

*   `site_callback` -一个 http(s) URL。
*   `email_table_name` -只是一个简单的表名，遵循 [DynamoDB 规则](http://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.NamingRulesDataTypes.html#HowItWorks.NamingRules)，比如`emails`。
*   `aes_password`-32 个字符的 AES 密钥。如果你手头没有，那就看看 [`keygen.js`](https://github.com/OliverCole/serverless-backend/blob/master/keygen.js) 。
*   从你的 Mailgun 账户。
*   `mailgun_domain_name` -您控制 DNS 的域或子域。
*   `from_email` -该域上的帐户。只是一个名字，不需要设置任何东西-无论如何 Mailgun 将工作！

Terraform 支持[模块](https://www.terraform.io/intro/getting-started/modules.html)的概念——旨在协同工作的可重用资源集合:例如，一组 web 服务器和一个负载平衡器。您可以在多个项目中使用它，甚至将它分离到单独的源代码控制或存储中。

我们将使用一个简单的模块将`environment_configs`映射转换成 SSM 参数存储中的条目。在与`terraform.tf`相同的目录下，新建一个名为`ssm_parameter_map`的目录，包含一个名为`ssm_parameter_map.tf`的文件，内容为:

```
variable "configs" {
  description = "Key/value pairs to create in the SSM Parameter Store"
  type        = "map"
}

variable "prefix" {
  description = "Prefix to apply to all key names"
}

variable "kms_key_id" {
  description = "ID of KMS key to use to encrypt values"
}

resource "aws_ssm_parameter" "configs" {
  count  = "${length(keys(var.configs))}"
  name   = "/${var.prefix}/${element(keys(var.configs),count.index)}"
  type   = "SecureString"
  value  = "${element(values(var.configs),count.index)}"
  key_id = "${var.kms_key_id}"
} 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到，这个模块有自己的变量，当您从自己的代码中调用这个模块时，这些值就会被设置。关于其余代码的更多细节，请查看 Gruntwork 关于[循环和 if 语句](https://blog.gruntwork.io/terraform-tips-tricks-loops-if-statements-and-gotchas-f739bbae55f9)的文章。

从`terraform.tf` :
调用您的模块

```
module "parameters" {
  source     = "/ssm_parameter_map"
  configs    = "${var.environment_configs}"
  prefix     = "${terraform.env}"
  kms_key_id = "${aws_kms_key.LambdaBackend_config.key_id}"
} 
```

Enter fullscreen mode Exit fullscreen mode

`source`参数指向模块代码，其他参数输入到您在`ssm_parameter_map.tf`中看到的变量中。`terraform.env`目前是字符串`default`，但当我们深入研究地形状态环境时，这将会改变。

因为 Terraform 模块可能位于任何地方，所以您需要运行`terraform init`来下载您的模块代码副本。这与您之前运行的下载 Terraform 的 AWS provider 的命令相同。应该是这样的:

```
λ terraform init 
Initializing modules... 
- module.parameters 
  Getting source "./ssm_parameter_map" 
```

Enter fullscreen mode Exit fullscreen mode

然后`terraform apply`照常。请记住，如果您在机器之间移动或 Git 工作副本，您将需要再次运行`terraform get`。

Terraform 是 pre v1.0 软件，模块中的`aws_ssm_parameter`资源有时会抛出`TooManyUpdates`错误。如果出现这种情况，就再`terraform apply`一次，并关注[这一期！](https://github.com/terraform-providers/terraform-provider-aws/issues/1082)

# 申请代码

现在我们要为我们的 Lambda 函数编写代码。一旦为部署而构建和打包了它，我们将切换回 Terraform 并部署它。

1.  创建一个名为`email_lambda`的子目录
2.  下载 [`index.js`](https://github.com/OliverCole/serverless-backend/blob/master/email_lambda/index.js) 到这个目录。
    *   在第 8 行，将 us-west-1 更改为与您配置的`aws`提供者相同的 AWS 区域。
    *   这是我们的主要应用程序代码
3.  将名为`email.html`的文件添加到包含电子邮件内容的目录中。
    *   如果你缺少想法，你可以使用这个[示例文件](https://github.com/OliverCole/serverless-backend/blob/master/email_lambda/email.html)。
4.  安装我们将与 npm 一起使用的电子邮件库。确保从内部进行此操作`email_lambda`:

```
npm install -prefix=./ nodemailer@4.0.1 nodemailer-mailgun-transport@1.3.5 aws-sdk@2.81.0 ssm-params@0.0.6 
```

Enter fullscreen mode Exit fullscreen mode

现在将整个目录压缩到`email_lambda.zip`，确保压缩文件的根目录包含文件(`index.js`等)，而不是名为`email_lambda`的目录。应该是这样的:

```
email_lambda.zip
├───etc
├───node_modules
├───email.html
└───index.js 
```

Enter fullscreen mode Exit fullscreen mode

如果你在 Windows 上安装了 7Zip，这个命令应该是`7z.exe a -r email_lambda.zip .\email_lambda\*`。

此时，您的目录结构应该如下所示:

```
├───.terraform
│   └───modules
│       └───03f77d1ff66d94c49e171247a4234cd8
├───email_lambda
│   ├───etc
│   └───node_modules
│       ├───nodemailer
│       │   └─── ...
│       ├───nodemailer-mailgun-transport
│       │   ├─── ...
│       └───ssm-params
│           └─── ...
├───ssm_parameter_map
├───email_lambda.zip
├───terraform.tf
├───terraform.tfstate
├───terraform.tfstate.backup
└───terraform.tfvars 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以将 Lambda 函数添加到`terraform.tf` :

```
resource "aws_lambda_function" "LambdaBackend_lambda" {
  filename         = "email_lambda.zip"
  function_name    = "LambdaBackend"
  role             = "${aws_iam_role.LambdaBackend_master_lambda.arn}"
  handler          = "index.handler"
  source_code_hash = "${base64sha256(file("email_lambda.zip"))}"
  runtime          = "nodejs6.10"
  timeout          = 15
  publish          = true

  environment {
    variables = {
      env = "${terraform.env}"
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

应用它，并继续进行[最后部分](https://dev.toterraforming-aws-a-serverless-website-backend-part-3)。