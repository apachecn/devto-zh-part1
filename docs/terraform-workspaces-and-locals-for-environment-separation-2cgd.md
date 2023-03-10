# Terraform 工作区和本地环境隔离

> 原文：<https://dev.to/diogok/terraform-workspaces-and-locals-for-environment-separation-2cgd>

Terraform 是一款出色的工具，可在遵循保持基础设施不变的良好实践的同时，调配和管理云基础设施的变更。

基础设施管理的一个常见需求是构建多个环境，如测试和生产环境，这些环境具有几乎相同的设置，但保留一些不同的变量，如网络和规模。

第一个帮助我们的工具是 [terraform workspaces](https://www.terraform.io/docs/state/workspaces.html) 。以前称为环境，它允许您在同一配置上创建不同且独立的状态。由于它与远程后端兼容，因此可以与您的团队共享该工作区。

作为一个例子，让我们使用下面的简单基础设施:

```
provider "aws" {
 region= "us-east-1"
}

resource "aws\_instance" "my\_service" {
 ami="ami-7b4d7900"
 instance\_type="t2.micro"
} 
```

现在我们已经定义了一个 aws ec2 实例，terraform apply 将启动您的测试服务器。

但这只是一个环境，在这个简单的例子中，人们可能认为简单地复制并调用一个服务“testing_my_service”和另一个服务“prod_my_server”就可以了，但这种方法会很快导致混乱，因为您的设置变得越来越复杂，并且添加了更多的资源。

你可以做的是用工作空间来分隔它们。

```
terraform workspace new production 
```

这样，您现在就进入了生产工作区。这一个将有相同的配置，因为我们在 terraform 的相同文件夹和模块中，但是没有创建任何东西。因此，如果您 terraform apply，它将创建另一个具有相同配置的服务器，但不会更改先前的工作空间。

要返回测试，您可以将工作区选择为 default，因为我们使用 default 作为测试环境，以确保我们不会错误地在生产环境中工作。

但是，很明显，测试和生产之间存在差异，第一种方法是在资源上使用变量和 if 开关。相反，一个更好的方法是使用最近引入的 [terraform locals](https://www.terraform.io/docs/configuration/locals.html) 来保持资源的逻辑贫乏:

```
provider "aws" {
 region= "us-east-1"
}

locals {
 env="${terraform.workspace}"

counts = {
 "default"=1
 "production"=3
 }

instances = {
 "default"="t2.micro"
 "production"="t4.large"
 }

instance\_type="${lookup(local.instances,local.env)}"
 count="${lookup(local.counts,local.env)}"
}

resource "aws\_instance" "my\_service" {
 ami="[ami-7b4d7900](https://console.aws.amazon.com/ec2/home?region=us-east-1#launchAmi=ami-7b4d7900)"
 instance\_type="${local.instance\_type}"
 count="${local.count}"
} 
```

与变量的主要区别在于，局部变量可以在其中而不是在资源中包含逻辑，而变量只允许值并将逻辑推入资源中。

要记住的一点是，terraform 正在快速发展，值得关注它的变化，以确保您充分利用它。