# Terraform 简介

> 原文：<https://dev.to/juan__wolf/introduction-to-terraform-21cj>

随着“云”的大量采用，不同的工具被用来简化开发/系统。管理员的生活。这些工具创造了一种新的处理 infra 的方式，即代码 IaC。老实说，云是一个好词，但让我们面对它，你只是在别人的服务器上运行虚拟机。使用这些工具的最大好处是，只需一个简单的 API 调用，您就可以提供、调配和销毁基础架构。编写一些脚本来自动填充 AWS 上的 VPC 是很棒的，但是调用 azure 来提供一些存储空间等等，最终你可能会“生病”。为什么不创建一个工具来代替你处理所有的 API 调用呢？这就是 terraform，让我们来看看如何使用这个令人敬畏的工具。

[![](img/52a181efa26e4cbe84246e45623072ee.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Yhh89qBz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.juanwolf.fr/post_preview/20180105_131244_terraform_logo.png)

> 免责声明:本教程将使用 AWS 作为云提供商。你在这个平台上花的每一分钱都是你的全部责任，你阅读这篇文章时会意识到，如果你不符合 AWS 免费层的条件，你将不得不花一些钱

## 起步婴儿步

我发现 terraform 的惊人之处在于工具给你的灵活性。你可以创建 100 个文件，它会把所有的文件连接起来，理解哪一部分依赖于另一部分等等，但是在拥有一个时髦的项目架构之前，让我们从简单的开始。

首先，让我们安装 terraform

### 安装

*   Mac: `brew install terraform`
*   Ubuntu: `sudo apt-get install terraform`
*   Windows:在 terraform 下载页面下载二进制文件，并将其添加到您的路径中。

### 设置您的 AWS 帐户

您需要进入 aws 控制台，设置一个特定用户来使用 terraform。进入`IAM`面板并创建一个用户。`my_terraform`

### 第一个项目

让我们创建一个 main.tf。如果你有资格使用 AWS 免费层，请随意使用，如果没有，你需要取出一些现金(抱歉)。您还需要一个 IAM 用户来获得访问密钥和秘密密钥。

我们将从零开始:

```
mkdir terraform_lab
cd terraform_lab
git init
git remote add origin git://my_git_repo.git 
```

Enter fullscreen mode Exit fullscreen mode

```
provider "aws" {
    access_key = "your_access_key"
    secret_key = "your_secret_key"
    region = "eu-west-1" # You can change the region for the one your prefer
}

# Nice copy pasta from the doc (https://www.terraform.io/docs/providers/aws/r/instance.html)
data "aws_ami" "ubuntu" {
  most_recent = true

  filter {
    name = "name"
    values = ["ubunimg/hvm-ssd/ubuntu-trusty-14.04-amd64-server-*"]
  }

  filter {
    name = "virtualization-type"
    values = ["hvm"]
  }

  owners = ["099720109477"] # Canonical
}

resource "aws_vpc" "default" {
    cidr_block = "172.16.0.0/16"
}

resource "aws_internet_gateway" "default"{
    vpc_id = "${aws_vpc.default.id}"
}

resource "aws_subnet" "default" {
    vpc_id = "${aws_vpc.default.id}"
    cidr_block = "172.16.0.0/16" # Just one big subnet covering the whole VPC. Of course do not use that in production.
}

resource "aws_security_group" "open_bar" {
    name = "open_bar"
    description = "Allow all connections inbound and outbound"
    vpc_id = "${aws_vpc.default.id}"
    ingress {
        from_port = "0"
        to_port = "0"
        protocol = "-1"
        cidr_blocks = ["0.0.0.0/0"]
    }
    egress {
        from_port = "0"
        to_port = "0"
        protocol = "-1"
        cidr_blocks = ["0.0.0.0/0"]
    }
}

resource "aws_instance" "simple_instance" {
    ami = "${data.aws_ami.ubuntu.id}"
    instance_type = "t2.micro"
    subnet_id = "${aws_subnet.default.id}"
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以直接看到你用 terraform 创建了什么组件，与一个满是卷曲的 bash 脚本相比，它更清晰，对吗？

现在让我们看看如何使用和删除这个文件。

#### 规划

在做出令人遗憾的事情之前，我们先看看 terraform 会怎么做。为此:

`terraform plan`

你应该有一个输出，显示我们要创建的每个资源。

我建议你在应用之前保存你做的每一个计划。以这种方式进行，你肯定计划的东西会被应用，而不是更多/更少。

为此，只需指定要保存计划的文件名/路径。

`terarform plan -out ./my_aws_plan`

在对计划的效果感到满意之后，让我们来应用它。

`terraform apply ./my_aws_plans`

如果您进入控制台，您应该看到您有一个全新的 vpc、一个子网和一个正在运行的实例。

在我向你们解释我们刚刚在 main.tf 文件中写了什么的时候，让我们销毁所有这些。

对于那个:`terraform destroy`。

还有多田，你回到你原来的状态，超级容易是不是？

### 刚刚发生了什么？

#### 地形图

在 terraform 中进行规划是测试、监督和监控 terraform 将会做什么的最佳方式。每次你想测试你的 terraform 项目->计划时，你都会看到语法/逻辑错误。不过，规划并不能防止供应商问题。例如，您创建一个范围为 172.0.0 的子网。<sup>1</sup><sub>32</sub>(这只是一个例子)如果你在子网内定义了一个 ec2 实例，其私有 ip 地址为 192.168.1.1，那么 terraform 只有在你应用了你的更改后才会得到错误，因为当 terraform 进行 API 调用时，AWS 会返回一个错误。

所以在我们的第一次尝试中，terraform 计划只创建了一些东西，这很正常，因为我们是从零开始的，但是 terraform 将如何知道如何从之前的运行中修改一些基础设施呢？如果您查看项目文件夹，您会看到一个 terraform.tfstate 文件。这是 terraform 用来了解您在云中 main.tf 中定义的组件的状态的。

如果存在此 terraform.tfstate 文件，当您制定计划时，terraform 将获取实际的基础架构配置，更新您的 tfstate 文件，并将 tfstate 中的内容与您在 main.tf 中定义的内容进行比较，并在您运行 apply 后提示您将会发生哪些变化。

#### 地形应用

此操作实际上会对您的云提供商执行 API 调用，如果未指定 plan_file，terraform 将在应用之前更新状态文件**，并在应用之后更新。这就是为什么你最好一直使用计划，以防你的架构在计划和应用之间被修改。**

### terra form 语法

如果您以前使用过其他 hashicorps 工具，那么 terraform 语法是一种常见的语法。他们使用。他们创建的 hcl 格式，这是 JSON 超级强大的注释和许多其他东西(好吧，我现在找不到任何其他东西，你说得有道理)。

在地形中，通常有两种定义:

*   `terraform_keyword name`
*   `terraform_keyword component_type component_id`

对于第一种语法，`<terraform_keyword>`可以是:

*   提供商:将向云提供商提供认证
*   变量:为您的地形应用范围创建一个变量
*   配置:用于配置地形，对后端配置有用(我们将在后面看到)

您的云提供商中使用 terraform 定义的所有东西都将具有相同的结构`<terraform_keyword> <component_type> <component_id> { ... }`

terraform 关键字可以在以下范围内变化:

*   资源:使用配置的提供者在云中创建一个新组件
*   数据:将检索关于现有组件的信息

根据您使用的云提供商，组件类型会有所不同。在这种情况下，terraform 文档是您的朋友(我不会一一列举，因为有 100 多个)

而要完成 component_id，可以是你喜欢的一切，你明白它是什么的时候。terraform 使用它来识别创建的不同组件。例如，如果我们已经定义了两种不同的 aws_instance，那么使用两种不同的 component_id(如“前端”或“后端”)就更好了

**警告**:如果你因为任何原因改变这个 id，terraform 将把它视为一个新的资源，并删除现有的资源，而不是修改它。

#### 提供者部分

在 terraform 中，您总是需要一个 provider 部分，您可以将该部分视为 terraform 配置的认证部分。这里我们设置了所有的配置，但是你可以在运行 terraform 的机器上用 env 变量来设置。

如果你为 azure、aws、vmware 等编写 terraform 文件，你当然可以使用多个提供者

#### 资源章节

在这里，您定义了您想要在云中创建的所有组件。它们是特定于提供者的，一旦创建，您就可以访问该资源将提供给您的不同变量。例如，创建 ec2 实例时，我们重用了之前创建的子网的 id。

同样，terraform 文档中对每个资源都有详细的描述。

#### 插值

如果您阅读了我在参考资料一节中写的内容，您就会理解用于定义 aws_instance 子网的野蛮语法是一个变量值。在 terraform 中，您可以使用带有\${}的字符串来为您定义的属性赋予一些逻辑。你可以重用你创建的组件的 id(就像我们一样)，你甚至可以使用循环，条件，地图，甚至一些数学函数。

### 我们的项目中彩

随着时间的推移，您的项目可能会变得更大，我的意思是，您的 main.tf 文件可能包含数千行，这使得它很难维护。让我们从一个直观的方法开始:将我们的 main.tf 文件分成几部分。

#### 切主. tf

Terraform 允许你创建任何你想要的文件，在一个计划或应用期间，它会尝试将当前文件夹中的所有`*.tf`文件连接成一个文件。它将解决不同文件之间的依赖关系，并应该能够重新创建 main.tf 文件，因为它是在我们把它切成碎片之前。我们只需要在如何做这件事上有逻辑。

让我们先将提供程序放在不同的文件中。让我们在当前文件夹中创建一个`providers.tf`文件，只包含我们的 aws 日志凭证。

```
# providers.tf

provider "aws" {
    access_key = "your_access_key"
    secret_key = "your_secret_key"
    region = "eu-west-1"
} 
```

Enter fullscreen mode Exit fullscreen mode

您甚至可以使用空凭据提交该文件，并确保您不会再推送它。

来测试一下吧！

`terraform apply`

**…**

好吧，你没有读整篇文章。我说为了测试我们使用**计划而不应用**。不管怎样，如果你掉进了陷阱，这也不会改变什么。

让我们继续我们的网络配置。让我们把 VPC·盖特威放进去。

```
# vpc.tf

resource "aws_vpc" "default" {
    cidr_block = "172.16.0.0/16"
}

resource "aws_internet_gateway" "default"{
    vpc_id = "${aws_vpc.default.id}"
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们对子网、安全组和实例进行同样的操作

```
# subnets.tf

resource "aws_subnet" "default" {
    vpc_id = "${aws_vpc.default.id}"
    cidr_block = "172.16.0.0/16" # Just one big subnet covering the whole VPC. Of course do not use that in production.
} 
```

Enter fullscreen mode Exit fullscreen mode

```
# security_groups.tf

resource "aws_security_group" "open_bar" {
    name = "open_bar"
    description = "Allow all connections inbound and outbound"
    vpc_id = "${aws_vpc.default.id}"
    ingress {
        from_port = "0"
        to_port = "0"
        protocol = "-1"
        cidr_blocks = ["0.0.0.0/0"]
    }
    egress {
        from_port = "0"
        to_port = "0"
        protocol = "-1"
        cidr_blocks = ["0.0.0.0/0"]
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
# instances.tf

# Yeah I put the ami with instances. No need elsewhere.
data "aws_ami" "ubuntu" {
  most_recent = true

  filter {
    name = "name"
    values = ["ubunimg/hvm-ssd/ubuntu-trusty-14.04-amd64-server-*"]
  }

  filter {
    name = "virtualization-type"
    values = ["hvm"]
  }

  owners = ["099720109477"] # Canonical
}

resource "aws_instance" "simple_instance" {
    ami = "${data.aws_ami.ubuntu.id}"
    instance_type = "t2.micro"
    subnet_id = "${aws_subnet.default.id}"
} 
```

Enter fullscreen mode Exit fullscreen mode

在重构的最后，你会得到一个更好的结构。这种结构将持续几次，直到你的基础设施开始变大。随着时间的推移，你会看到它的一些限制，比如复杂的依赖关系，甚至是 SOC(关注点分离)

所以模块来了。

## 模块

使用模块，你可以在模块内部分离你的基础设施的组件，允许你防止你的基础设施定义中的任何重复。当你想要扩展你当前基础设施的某些组件或者当你想要重构“所有的主人”时，这真的很方便。

### 建筑

要从模块开始，您需要在项目的根目录下创建一个 modules 文件夹，并以您想要创建的模块的名称创建一个文件夹。在最后一个文件夹中，你只需要创建三个文件，你就已经创建了你的第一个模块！这些文件是:

*   这个文件包含了你想要参数化你的模块的所有变量。例如，您将传递一些 vpc_id 或一些 ami。

*   像我们的第一个 main.tf 一样，它将包含你的模块的所有资源定义。

*   outputs.tf:包含执行模块后需要的所有变量。最常见的用途是*输出*未来创建的实例的公共 IP。

### 使用它

在我们的例子中，infra 非常简单，所以这个模块也很简单。我们将把我们的 simple_instance 放在模块中。但是在某种程度上，我们可以配置它在哪个子网、哪个 az 上。

让我们开始创建模块。

```
mkdir -p modules/my\_cluster\_of\_instances touch modules/my\_cluster\_of\_instances/{main,variables,output}.tf 
```

Enter fullscreen mode Exit fullscreen mode

让我们把“简单实例”的定义放在 main.tf 中

```
# main.tf

resource "aws_instance" "simple_instance" {
    ami = "${var.ami_id}"
    instance_type = "${var.instance_type}"
    subnet_id = "${var.subnet_ids}"
    count = "${cluster_size}"
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你看到了，我把变量改成了 variable.tf 文件中定义的东西。我添加了一个计数属性，以防我们想要扩展这个模块

现在我们来配置变量:

```
# variables.tf

variable "subnet_ids" {
    description = "The list of subnet id"
}

variable "cluster_size" {
    description "The number of instance you want"
    default = 1
}

variable "instance_type" {
    description = "The type of instance you want"
    default = "t2.micro"
}

variable "ami_id" {
    description = "The AMI to use on these instances"
}

# output.tf

output "private_ips" {
    value = ["${aws_instance.simple_instance.*.private_ip}"]
} 
```

Enter fullscreen mode Exit fullscreen mode

最后一个使用了通配符，因为我们不知道模块中将创建多少个实例。它意味着"*输出*实例的私有 IP 列表"

现在让我们在项目的根目录下创建一个 main.tf，调用这个模块:

```
# main.tf

# Everything we had a bit earlier ... (vpcs, subnets until the instance resource)

module "awesome_instance" {
    module_path = "modules/my_cluster_of_instances"
    ami_id = "${data.aws_ami.ubuntu.id}"
    subnet_id = "${aws_subnet.default.id}"
    instance_type = "t2.micro" # No need of this line as there's a default value
    cluster_size = 2 # Here we override the default value
}

aws_security_group_rule "a_simple_sg_rule" {
    security_group_id = "${}"
    type = "ingress"
    from = 0
    to_port = 0
    protocol = -1
    cidr_block = ["${module.awesome_instance.private_ips}"] # Using here the output of our module
} 
```

Enter fullscreen mode Exit fullscreen mode

在命令行:

```
terraform get # Will create reference to our module
terraform plan # Should destroy what we had before 
```

Enter fullscreen mode Exit fullscreen mode

遗憾的是，terraform 不会理解这个模块代表你的旧实例，并且会试图删除你的旧实例来放置这两个消息。

### 关于项目结构的结论

到目前为止，模块结构是我目前遇到的最好的。我通常在项目的根目录下有几个文件夹:

1.  模块/
2.  env1/
3.  env2/

并使用 envx 作为环境之间的适当隔离。当环境之间有很多差异时，这真的很方便。

## 其他命令同 terraform

为了结束这篇*超* *长*的文章，我将简要地讲一下我们在文章中没有看到的命令。

### 污点

在 terraform 中，你可以污染一些资源，这样它们会在下次应用时被破坏。如果你想从零开始使用一些组件，这真的很有用。

在我们的模块示例中，我们会这样使用它:

`terraform taint -module=awesome_instance instance.0`

因此，您需要指定从哪个模块污染资源。在那个资源名称之后是哪个。

> **警告**:根据[这个 github 问题](https://github.com/hashicorp/terraform/issues/)还不支持通配符

### 图表

如果您的笔记本电脑上安装了 graphviz，您可以创建一个您定义的 terraform 资源的图表。

### 导入

如果您在 UI 中创建了一些资源，在 tf 项目中添加它们的定义是不够的。您需要使用 import 命令将其添加到 terraform 状态。

例如，如果我们在 ui 中创建了 ec2 实例。我们会像这样将它添加到 tfstate 文件中。

```
terraform import aws\_instance.my\_instance the\_id\_of\_the\_instance 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

关于 terraform 还有很多要说的，但我会在这里停下来，因为我已经写了将近 2500 个字…我们已经看到，Terraform 是一个用代码管理基础设施的伟大工具。他们的。与简单的 json 定义相比，tf 格式确实是个好东西。terraform 非常宽容，所以你可以从一个简单的项目开始，以数百个模块相互调用结束，这使得该工具的应用变得非常容易。不管怎样，我让你享受你的`terraform destroy`

在这上面写好代码！你好！