# 地形化 AWS:无服务器网站后端，第 1 部分

> 原文：<https://dev.to/olivercole/terraforming-aws-a-serverless-website-backend-part-1>

如果您可以使用代码或文本为您的云应用程序定义所有基础架构，自动应用您的设计和更改，然后在源代码控制中与您的团队协作，会怎么样？

*这是 [Terraform](https://www.terraform.io/) 的承诺，一个来自[hashi corp](https://www.hashicorp.com/)T5 的基础设施即代码工具*

# 简介

Rob Sherling 写了一篇关于如何在无服务器基础设施上构建网站后端的文章。本文将带您了解使用 Terraform 构建 Rob 的基础设施设计的第一阶段，并解释一些 Terraform 概念和规则。提前感谢 Rob，他允许我在他的代码和设计的基础上写这篇文章！

这篇文章和 Rob 的原文一样，假设:

*   您有一个 AWS 帐户，知道如何使用它，并且对一些小额费用感到满意(尽管这通常属于免费级别)。
*   您已经安装了 [AWS CLI](https://aws.amazon.com/cli/) 并配置了。
*   你有一个域名可以用于这个项目。
*   您已经熟悉了基本的 Javascript——不要担心，我们只是修改一些现有的代码。
*   你至少听说过下面这些(如果没有，读一点你不知道的):
    *   [AWSλ](https://aws.amazon.com/lambda/)
    *   [API 网关](https://aws.amazon.com/api-gateway/)
    *   [云观察](https://aws.amazon.com/cloudwatch/)
    *   [DynamoDB](https://aws.amazon.com/dynamodb/)
    *   [KMS](https://aws.amazon.com/kms/)
    *   [我是](https://aws.amazon.com/iam/)
    *   邮件服务[邮件枪](https://www.mailgun.com/)
    *   API 工具[邮递员](https://www.getpostman.com/)

你以后可能会需要更多，但是如果你有了以上的东西，你今天就可以建造一些东西！

我们将利用所有这些不同的服务和一些应用程序代码来构建一个完整的应用程序。如果您更喜欢通过在 EC2 上启动一些虚拟机来学习基础知识，您可能更喜欢 David Schmitz 关于使用 Terraform 进行云部署的文章。

### 那么什么是 Terraform 呢？

> Terraform 是一个安全有效地构建、更改和版本控制基础设施的工具。Terraform 可以管理现有的和受欢迎的服务提供商以及定制的内部解决方案。
> 
> 配置文件描述了运行单个应用程序或整个数据中心所需组件的平台化。Terraform 生成一个执行计划，描述它将做什么来达到期望的状态，然后执行它来构建所描述的基础设施。随着配置的变化，Terraform 能够确定发生了什么变化，并创建可以应用的增量执行计划。

换句话说，你用人类可读的配置/代码来描述你的应用程序基础设施，Terraform 处理如何在你的云提供商那里实现的所有细节。

Terraform 和 CloudFormation 都允许您在 AWS 上实现类似的结果，但 Terraform 支持的云提供商不仅仅是 AWS，还允许您处理多个 AWS 帐户等场景，或将不同的云提供商组合到一个部署的应用程序中。

### 我们实际上在建造什么？

我们将为一个用于营销活动的网站制作后端——它将为潜在客户收集 Twitter 句柄和电子邮件地址，并允许您向他们发送消息。为了时髦和现代，我们将通过完全使用 AWS 云服务(主要是 Lambda 和 DynamoDB)来降低成本和维护，使后端 *' [无服务器](https://aws.amazon.com/serverless/) '* 。

在本系列文章的第 1 部分中，我们将只讨论最初的电子邮件捕获和存储。

以下是主要部分的组装方式:

[![Serverless backend AWS diagram](img/529873b2ef3d83247677544b7dbf4408.png "Serverless backend AWS diagram")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7jfWvjv9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4ndc28tq72uqtj6jtqr0.png)

### 我需要什么？

您将需要一些东西来完成整个系列，但是对于第 1 部分，您所需要的是以下内容:

#### 地形

[下载它](https://www.terraform.io/downloads.html)，并将其放置在您的路径中。

#### 邮递员

[安装它](https://www.getpostman.com/)。我们将用它来测试你的后端 API。

#### 邮件枪账号

注册一个[免费账户](https://www.mailgun.com/)。我们将用它来发送电子邮件。不要链接你的域名(如果你已经链接了，删除它)。确保你设置了一些[授权接收者](https://app.mailgun.com/app/account/authorized)在测试时使用。

#### Node.js

[安装它](https://nodejs.org/en/)。我们搭建的基础设施也将支持 Python、Java 和 C#(。NET Core)，但本系列文章中的示例代码都是 Node.js。

#### 可选:文本编辑器插件

如果你已经升级到比记事本更高级的东西(你真的应该升级！)，安装语法突出显示和编辑器设置可以让您的一天有很大的不同:

*   Atom [语法高亮显示](https://atom.io/packages/language-terraform)
*   VS 代码[语法高亮、林挺、格式化和验证](https://marketplace.visualstudio.com/items?itemName=mauve.terraform)
*   升华文本[语法高亮和代码片段](https://github.com/alexlouden/Terraform.tmLanguage)

# 入门

在此过程中，我将介绍各种 Terraform 和 AWS 概念，并链接到它们的文档。你可以按照你喜欢的任何顺序排列 Terraform 文件——我选择这个顺序只是为了慢慢引入新的概念。

### 设置

为这个项目创建一个新目录，并在那里创建一个名为`terraform.tf`的新文本文件。

在我们开始在 AWS 中设置基础设施之前，我们需要告诉 Terraform 中内置的 [AWS **提供者**](https://www.terraform.io/docs/providers/aws/index.html) 如何与 AWS API 对话。我们现在需要覆盖的是 [AWS 地区](http://docs.aws.amazon.com/general/latest/gr/rande.html)——提供商将从您已经配置的 AWS CLI 中获取您的其余凭证。不要太担心这个变量，我们稍后会谈到它。

```
variable "region" {
  default = "eu-west-1"
}

provider "aws" {
  region = "${var.region}"
} 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到代码看起来很像你习惯的配置文件，例如 JSON 或 YML。如果愿意，您可以将区域值设置为最接近的值。

在**提供者**之后，围绕**资源**的概念构建 Terraform。通常，这些对应于您的云提供商提供的“东西”，如虚拟机或服务器、数据库、存储的文件等。有时，它们实际上指的是两个资源之间的链接或关联，或者其他配置。

### 数据库

我们将从 DynamoDB 表开始——它与 Terraform 资源有 1:1 的关系。将此添加到您的`terraform.tf` :

```
resource "aws_dynamodb_table" "emails" {
  name           = "emails"
  read_capacity  = 2
  write_capacity = 2
  hash_key       = "email"

  attribute {
    name = "email"
    type = "S"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们来分解一下:

*   [`aws_dynamodb_table`](https://www.terraform.io/docs/providers/aws/r/dynamodb_table.html) 是 AWS 提供者提供的资源。
*   第一个`emails`是这个资源的名字——但是只在 Terraform 中。你可以尽可能的笼统或者描述性，但是就像在任何软件开发中一样，通过阅读名字就能理解某个东西是什么是一个很好的实践。
*   第二个`emails`是实际的 DynamoDB 表名——您将在 AWS 控制台中看到它，并在代码中引用它。
*   其余的参数是特定于资源的——如果您想了解更多，请查阅文档。

### 初始化

Terraform 基于可插拔设计——实际上与云提供商对话并创建资源的组件会自动下载并存储在您的项目目录中。
所以在调用 AWS 之前，您需要告诉 Terraform 下载 AWS 提供程序:

```
λ terraform init 

Initializing provider plugins... 
- Checking for available provider plugins on https://releases.hashicorp.com... 
- Downloading plugin for provider "aws" (1.11.0)... 

The following providers do not have any version constraints in configuration, 
so the latest version was installed. 

To prevent automatic upgrades to new major versions that may contain breaking 
changes, it is recommended to add version = "..." constraints to the 
corresponding provider blocks in configuration, with the constraint strings 
suggested below. 

* provider.aws: version = "~> 1.11" 
```

Enter fullscreen mode Exit fullscreen mode

### 计划并应用

下一步是让 Terraform 建立一个你所要求的改变计划。继续运行`terraform plan`——Terraform 中的 AWS 提供商将查看您当前的 AWS 设置，并计划好部署您的基础设施需要做什么。它应该是这样的:

```
λ terraform plan
[...]
An execution plan has been generated and is shown below. 
Resource actions are indicated with the following symbols: 
  + create 

Terraform will perform the following actions: 

  + aws_dynamodb_table.emails 
      id:                        <computed> 
      arn:                       <computed> 
      attribute.#:               "1" 
      attribute.3226637473.name: "email" 
      attribute.3226637473.type: "S" 
      hash_key:                  "email" 
      name:                      "emails" 
      read_capacity:             "2" 
      server_side_encryption.#:  <computed> 
      stream_arn:                <computed> 
      stream_label:              <computed> 
      stream_view_type:          <computed> 
      write_capacity:            "2" 

Plan: 1 to add, 0 to change, 0 to destroy. 
```

Enter fullscreen mode Exit fullscreen mode

这说明了什么？：

*   将显示您的新资源`aws_dynamodb_table.emails`，以及它的所有属性。如`+`所示，这是一个应该创建的资源。
*   创建表格时，标有`<computed>`的值将由 AWS 分配。记住这只是 Terraform 的行动计划，所以这些还不存在。

顺便说一下，如果你的 AWS 账户中已经设置了其他东西，你会注意到它不会出现在这里。您可以安全地混合手动管理的资源和 Terraform 资源，甚至多个 Terraform 项目。

此时，您可以继续使用`terraform apply` :

```
λ terraform apply

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + aws_dynamodb_table.emails
      id:                        <computed>
      arn:                       <computed>
      attribute.#:               "1"
      attribute.3226637473.name: "email"
      attribute.3226637473.type: "S"
      hash_key:                  "email"
      name:                      "emails"
      read_capacity:             "2"
      server_side_encryption.#:  <computed>
      stream_arn:                <computed>
      stream_label:              <computed>
      stream_view_type:          <computed>
      write_capacity:            "2"

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_dynamodb_table.emails: Creating...
  arn:                       "" => "<computed>"
  attribute.#:               "" => "1"
  attribute.3226637473.name: "" => "email"
  attribute.3226637473.type: "" => "S"
  hash_key:                  "" => "email"
  name:                      "" => "emails"
  read_capacity:             "" => "2"
  server_side_encryption.#:  "" => "<computed>"
  stream_arn:                "" => "<computed>"
  stream_label:              "" => "<computed>"
  stream_view_type:          "" => "<computed>"
  write_capacity:            "" => "2"
aws_dynamodb_table.emails: Still creating... (10s elapsed)
aws_dynamodb_table.emails: Creation complete after 14s (ID: emails)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed. 
```

Enter fullscreen mode Exit fullscreen mode

如果您厌倦了键入`yes`，您可以随时添加一个`-auto-approve`标志来跳过确认。

您可能已经注意到，该表的吞吐量为 2。对于这个项目来说，这可能有点过了，所以编辑`terraform.tf`使其为 1，而`terraform plan`。

```
~ aws_dynamodb_table.emails
    read_capacity:  "2" => "1"
    write_capacity: "2" => "1" 
```

Enter fullscreen mode Exit fullscreen mode

这一次你可以看到桌子是时候改变了(标有`~`)——terra form 不会破坏和重建它。更改的参数将显示在下面。在这一点上，`terraform apply`应该是自明的:

```
aws_dynamodb_table.emails: Modifying... (ID: emails)
  read_capacity:  "2" => "1"
  write_capacity: "2" => "1" 
```

Enter fullscreen mode Exit fullscreen mode

如果你运行`terraform state list`，你会在你的 AWS 账户中看到 Terraform 管理的所有东西的列表。您可以检查`terraform state show aws_dynamodb_table.emails`以获得特定资源的详细信息。

# 权限和安全

## IAM

我们将在 Lambda 函数中运行后端服务器逻辑，但在构建之前，我们需要设置一个 [IAM 角色](https://www.terraform.io/docs/providers/aws/r/iam_role.html)并为其附加一些[策略](https://www.terraform.io/docs/providers/aws/r/iam_role_policy_attachment.html)。这将赋予我们的函数访问其他 AWS 服务的权限。

```
data "aws_caller_identity" "current" {}

resource "aws_iam_role" "LambdaBackend_master_lambda" {
  name = "LambdaBackend_master_lambda"
  path = "/"

  assume_role_policy = <<POLICY
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "lambda.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
POLICY
}

resource "aws_iam_role_policy_attachment" "LambdaBackend_master_lambda_AmazonDynamoDBFullAccess" {
  role       = "${aws_iam_role.LambdaBackend_master_lambda.name}"
  policy_arn = "arn:aws:iam::aws:policy/AmazonDynamoDBFullAccess"
}

resource "aws_iam_role_policy_attachment" "LambdaBackend_master_lambda_CloudWatchFullAccess" {
  role       = "${aws_iam_role.LambdaBackend_master_lambda.name}"
  policy_arn = "arn:aws:iam::aws:policy/CloudWatchFullAccess"
}

resource "aws_iam_role_policy" "ssm_read" {
  name = "ssm_read"
  role = "${aws_iam_role.LambdaBackend_master_lambda.id}"

  policy = <<POLICY
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
          "ssm:DescribeParameters"
      ],
      "Resource": "*"
    },
    {
        "Effect": "Allow",
        "Action": [
            "ssm:GetParameters"
        ],
        "Resource": "arn:aws:ssm:${var.region}:${data.aws_caller_identity.current.account_id}:parameter/${terraform.env}/*"
    }
  ]
}
POLICY
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一些新的语法，我们来解释一下:

*   `<<`语法允许您包含多行字符串或文档。在这种情况下，我们将其用于 [IAM 政策文件](http://docs.aws.amazon.com/lambda/latest/dg/access-control-identity-based.html)。在`<<`之后，我们有`POLICY`，告诉 Terraform 从该点开始读取，直到下一个`POLICY`标记。
    *   名字`POLICY`并不特别，什么都可以。
    *   如果您曾经编写过 Unix 终端的脚本，这可能听起来很熟悉。
    *   正如您可能已经猜到的，尖括号内的任何内容都不是针对 Terraform 的，所以如果您不理解它的意思，现在也不用担心。
*   如果您不想将 AWS 策略文档保存在 Terraform 代码中，可以使用 [`file()`内置函数](https://www.terraform.io/docs/configuration/interpolation.html#file-path-)从磁盘加载它们。
*   `role`参数依赖于地形[插值](https://www.terraform.io/docs/configuration/interpolation.html)。这是一种奇特的说法，它允许您从其他地方获取值，无论是另一个资源，还是一些静态配置。
    *   如果你习惯于一种面向对象编程语言，可以把它想象成访问类成员。
    *   我们在这里真正做的是确保策略附件有效，不管您如何命名`aws_iam_role`。这是一个[不要重复自己](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)的例子，在任何语言中都是一个很好的练习。
*   现在不要担心`data`行——我们将在第二部分中讨论它。

当你高兴的时候，继续做这个，继续进行下一部分。