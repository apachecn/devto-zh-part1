# 地形:作为代码的基础设施——第一部分

> 原文：<https://dev.to/cyberomin/terraform-infrastructure-as-code---part-i>

#### 介绍

在过去的几个月里，我痴迷于自动化、工作流和代码基础设施。这种痴迷让我探索了 Ansible 和一点点 Chef 这样的工具，以及如何更好地将它们应用到我的日常工作中。

在过去的几周里，我一直在试验 Harshicorp 的 Terraform，我必须说，我印象深刻。在这篇文章中，我想分享我的发现，并记录我为子孙后代所学到的东西。

> PS:我已经创建了一个自举的流浪者盒子，并提供了地形。您可以在这里克隆[存储库，然后继续操作。](https://github.com/cyberomin/terraform)

Terraform 和 Harshicorp 的其他产品一样，都有可以从 CLI 运行的基本命令。但是最常用的 Terraform 命令是`terraform apply`。这是实际上允许 Terraform 运行并与我们的提供者通信的命令(稍后将详细介绍)。`terraform apply`命令发送给我们的提供者，并提供我们在 Terraform 文件中声明的资源。简单来说，它建立或改变我们的基础设施。

虽然`terraform apply`命令很棒，但问题是它不能给你正在做什么的早期反馈，幸运的是，Terraform 提供了另一个命令`terraform plan`来做这件事。它让我们看到基础设施执行计划。

#### Terraform 语法- HCL

Terraform 的代码是用 Harshicorp 的专有语言编写的，该语言称为 Hashicorp 配置语言(HCL)。HCL 是一种结构化的配置语言，旨在对机器友好和人类可读。它主要面向 DevOps，对于 Terraform，它的语法允许我们将基础设施描述为代码。所有地形代码都写在一个扩展名为`.tf`的文件中。

在我们使用 Terraform 并探索它的威力之前，我们需要声明一个提供者。这是每个 Terraform 程序的入口。在这篇文章发表的时候，他们有超过 10 个不同的平台提供商，包括:AWS、数字海洋、谷歌云等。如需完整和最新的提供商列表，请访问平台提供商[文档页面](https://www.terraform.io/docs/providers/index.html)。

声明一个提供者很简单，您可以从关键字`provider`开始，并提供提供者的名称，例如；`aws`、`digitalocean`、`google`等。

```
provider "digitalocean" {
    # todo
} 
```

*代码 I*

上面的代码是如何声明 Terraform 提供者的。就这么简单。上面的声明本身没有任何作用，而且用途非常有限。为了利用它的能力并开始与数字海洋云通信，我们必须提供一个有效的数字海洋令牌。这就是 Terraform 能够与我们的数字海洋云通信的方式。

您可以从您的数字海洋帐户中获得一个令牌，只需登录并生成一个即可。记住也要保持这个令牌的安全，因为如果它落入坏人之手，可能会发生不好的事情。*插入如何生成数字海洋令牌的图片*

我们将扩展我们的初始代码 Code I，并添加 DO 标记。

```
provider "digitalocean" {
    token = "xxxx-xxxxx-xxxx-xxxx-xxxx"
} 
```

*代码二*

我们在这里的工作还没有完成，我们只是告诉 Terraform 我们希望与数字海洋合作，仅此而已。这本身没有任何作用，我们需要创造一些数字海洋水滴。Terraform 有一个资源的概念，资源通常是你的云托管服务提供的服务，用 Terraform 的话说就是提供商。这些资源包括但不限于:虚拟机(EC2、droplets)、负载平衡器、数据库、缓存等。为了创建一个数字海洋水滴，我们将声明一个水滴资源，并传递一些参数，如 image type—Ubuntu、CentOS 等，name—机器的主机名，region—我们希望创建该资源的 DO 区域，size—水滴的大小。

```
provider "digitalocean" {
    token = "valid digital ocean token"
}

resource "digitalocean_droplet" "web" {
    image = "ubuntu-16-04-x64"
    name = "web-1"
    region = "lon1"
    size = "1gb"   
} 
```

*代码三*

上面声明的代码 III 的可读形式翻译成*“在伦敦 1 区创建一个 64 位的 Ubuntu 16.04 droplet，大小为 1gb，主机名为 web-1。”值得注意的是，上面资源声明中的一些东西是标准的。`resource`关键字是标准的，`digitalocean_droplet`是标准的，这是 Terraform 如何表示数字海洋的水滴——它因其他云提供商而异，例如，`aws_instance`表示 AWS 的 EC2，`google_compute_instance`表示谷歌的 VM。`web`这个词是任意的，它在这个声明中作为一个标识符，因此，你可以选择任何你喜欢的名字。花括号内的其他声明是标准的和必需的。有关如何为其他提供者声明它的信息，请参考 Terraform 文档。*

使用上面的代码，我们成功地声明了一个有效的 Terraform 提供者，并创建了一个相关的资源。为了运行这个命令来查看它的效果，我们将在控制台上运行`terraform apply`。此时，Terraform 将启动与数字海洋的通信链接，如果一切顺利，将为我们创建一个有效且随时可用的 droplet。这是一种相当简单的做事方式，我们将在本文中更深入地探讨。

**变量**

在*代码 II* 中，我们创建了一个数字海洋提供商，并为其提供了一个 API 令牌。虽然这完成了工作，但并不是处理这个问题的最好方法。这就是变量概念的由来。像许多传统的编程语言一样，Terraform 也有一个变量的概念，尽管声明方式不同。

在 ES6 for instance 中，变量可以用关键字`const`或`let`声明。例如

```
const name = “Bob Jones”
let age = 70 
```

代码四

但是在陆地上事情有点不同。每个变量都用关键字`variable`断言，后跟变量名和一组参数。Terraform 的变量有两种不同的风格；输入和输出变量。输入变量用于将值发送到 Terraform，而输出变量将 Terraform 的结果打印到 stdout。输入变量可以以不同的格式发送；命令行，来自一个文件和一个环境变量。

要为变量创建赋值数据，我们将从代码 v 中的关键字`variable`开始。

根据代码 V 中的定义，如果我们使用`terraform apply`运行 Terraform 代码，Terraform 将提示我们为`name`变量输入一个值，而不会为第二个变量`token`输入值。这是因为，在第二个声明中，我们已经为这个变量提供了一个默认值，它是我们的数字海洋 API 令牌，Terraform 从那里选择它。

如果我们需要在任何地方使用这个变量，我们将不得不像这样调用它`“${var.token}”`，所以回到代码 II，我们可以将声明修改为这样的格式:provider " digital ocean " { token = " $ { var . token } " } Code VI

这里的优点是，我们可以在多个位置使用这个变量，而不必在多个位置重复 API 令牌本身。这为我们如何管理代码提供了巨大的力量。

输出变量遵循与输入变量相同的模式，唯一的区别是输出变量使用`value`代替`default`。下面是输出变量的示例声明。输出" IP " { value = " $ { digital ocean _ droplet . web . IP v4 _ address } " }代码 VII。

代码 VII 中的声明告诉 Terraform 将我们的 droplet 的 IPV4 地址打印到控制台。到目前为止，我们已经能够创建一个数字海洋水滴，这很好，但现在的问题是，我们不能 ssh 到我们新造的机器，这是一个主要问题，肯定会给我们带来问题。要解决这个问题，我们需要将 SSH 公钥添加到 droplet 中。Terraform 为我们提供了一个名为`digital ocean_ssh_key`的 SSH 资源。要使用该资源，我们将其声明如下:

```
resource "digitalocean_ssh_key" "default" {
    name = "SSH Key Credential"
    public_key = "${file("/home/vagrant/.ssh/id_rsa.pub")}"
} 
```

代码八

随着 SSH 密钥资源的引入，我们需要将它链接到我们的 droplet。这样，我们就可以使用我们的私钥进行 SSH。要做到这一点，我们必须修改代码 III 中的代码。代码 VIII uploads 中的代码允许我们的 droplet 使用 SSH 公钥。另外，请注意，我们在这里没有复制和粘贴我们的 SSH 密钥，而是使用了一个名为`file`的 Terraform 内置函数。file 函数让我们从一个路径中读取文件。它有一个基本语法`${file(“path/to/file")}`。

> 如果您正在使用我在本文中提供的流浪者盒子，我强烈建议您生成一个新的 SSH 密钥，因为这个盒子没有这个密钥。生成 SSH 密钥很简单，只需在您的终端`ssh-keygen -t rsa -b 4096 -C “your email”`上运行这个命令，并按照屏幕上的信息进行操作。我强烈建议您不要为您的 SSH 密钥设置密码。

```
provider "digitalocean" {
    token = "xxxx-xxxxx-xxxx-xxxx-xxxx"
}

resource "digitalocean_droplet" "web" {
    image = "ubuntu-16-04-x64"
    name = "web-1"
    region = "lon1"
    size = "1gb"
    ssh_keys = ["${digitalocean_ssh_key.default.id}"]
} 
```

*代码九*

把所有的东西放在一起，我们会得到这样的东西:

```
variable "token" {
    default = "xxxx-xxxxx-xxxx-xxxx-xxxx"
}

provider "digitalocean" {
    token = "${var.token}"
}

resource "digitalocean_ssh_key" "default" {
    name = "SSH Key Credential"
    public_key = "${file("/home/vagrant/.ssh/id_rsa.pub")}"
}

resource "digitalocean_droplet" "web" {
    image = "ubuntu-16-04-x64"
    name = "web-1"
    region = "lon1"
    size = "1gb"
    ssh_keys = ["${digitalocean_ssh_key.default.id}"]
}

output "ip" {
    value = "${digitalocean_droplet.web.ipv4_address}"
} 
```

*代码 X*

如果我们再次运行`terraform apply`,我们将得到类似如下的输出

```
...

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

Outputs:

    ip = 46.XX.XXX.XXX 
```

**控制和条件**

与过程语言不同，Terraform 使用声明性语言模式。如果你想创建一个资源，比如说三次，你将把它们包装在一个 for 循环中，但是在 terraform 中，你将使用一个像`count`这样的元参数

值得注意的是，每个 terraform 资源可用的`count`参数都有一个从零开始的索引，类似于传统编程语言中的数组。所以如果你有这样的资源声明

```
resource "digitalocean_droplet" "web" {
    count = 4
    image = "ubuntu-16-04-x64"
    name = "web.${count.index}"
} 
```

*代码 XI*

从上面的代码 XI，我们将有 3 个名称创建的 web 服务器；web.0、web.1、web.2、web.3。请注意，names 参数使用插值语法。

如果我们想在使用某个东西之前检查它的真实性，我们可以使用另一个条件，它类似于常规编程语言中的路线操作符。它遵循模式`condition ? trueval : falseval`。让我们声明一个只有满足特定条件时才可用的资源。

```
resource "digitalocean_loadbalancer" "pubic" {
    count = "${var.env == "production" ? 1 : 0}"
} 
```

*代码 XII*

根据代码 XII 中的声明，DO 负载平衡器将仅在环境是生产环境时存在。Terraform 也支持类似`!=, >, <, >=, <= && || !`的操作。

*免责声明*

*在遵循本文中列举的例子时，请记住这是有代价的。当你运行`terraform apply`并在你的提供商端创建一个真正的资源时，他们几乎立刻就开始向你收费。作为一个警告，这仅适用于非生产环境，总是尝试在你自己之后清洁。为此，Terraform 为我们提供了一个非常方便的命令`terraform destroy`。`terraform destroy`命令实际上是回到你的提供商那里，删除/销毁你创建的每一个资源。这是一个单向命令，无法撤消，所以我强烈建议您在个人或实验环境中这样做。*

*本帖原载于[cyber omin . github . io](http://cyberomin.github.io/engineering/2017/05/29/terraform-introduction.html)T3】*