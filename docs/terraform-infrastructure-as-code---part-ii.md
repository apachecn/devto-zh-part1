# 地形:作为代码的基础设施-第二部分

> 原文：<https://dev.to/cyberomin/terraform-infrastructure-as-code---part-ii>

在第一部分中，我使用 Terraform 向我们介绍了 IAC(作为代码的基础设施)的概念，我们探索了 Terraform 的神奇之处。虽然我们在第一部分中用于提供简单服务器的代码工作得非常好，但我们最终提供的系统很难是可伸缩的系统，我不推荐将其用于生产。这里的理由很简单，为整个应用程序运行一台服务器几乎就像在加油站点燃一根火柴一样糟糕，糟糕的事情可能而且肯定会发生。我强烈建议你不要做这种安排。

在 IAC 系列的第二部分中，我希望我们在第一部分的基础上构建一个半高度可用的系统。在第一部分的代码 X 中，我们用下面的代码
创建了一个服务器

```
resource "digitalocean_droplet" "web" {
  image = “ubuntu-16-04-x64”
  name = “web-1”
  region = “lon1”
  size = “1gb”
  ssh_keys = ["${digitalocean_ssh_key.default.id}"]
} 
```

Enter fullscreen mode Exit fullscreen mode

代码 v

现在让我们扩展这段代码，创建两个 web 服务器。为此，我们将添加一个名为`count`的元参数。count 参数可以添加到任何资源中，它只是根据计数值创建更多的已声明资源。例如，我们将添加`count = 2`来创建两个 web 服务器。

```
resource “digitalocean_droplet “web {
  image = “ubuntu-16-04-x64”
  name = “web-1”
  count = 2
  region = “lon1”
  size = “1gb”
  ssh_keys = ["${digitalocean_ssh_key.default.id}”]
} 
```

Enter fullscreen mode Exit fullscreen mode

代码二

注意到上面代码中对`count = 2`的介绍了吗？这就是我们如何创建 2 个 web 服务器。如果我们想要创建 10 个 web 服务器，我们所需要做的就是将 count 的值改为 10，如果我们需要 20 个服务器，我们将 count 的值改为 20，你明白了吧。

为了构建半高可用性系统，我们要做的下一件事是创建一个负载平衡器，并将流量分配给我们所有的服务器。Terraform 为我们提供了一个`digitalocean_loadbalancer`资源，我们会好好利用它。添加负载平衡器很简单，我们将声明一个负载平衡器资源，给它一个名称，选择一个区域，应用流量转发规则，从负载平衡器添加一个健康检查到连接的机器，最后，将我们的数字海洋液滴连接到这些负载平衡器。就这么简单。

```
resource “digitalocean_droplet “web {
  image = “ubuntu-16-04-x64”
  name = “web-1”
  count = 2
  region = “lon1”
  size = “1gb”
  ssh_keys = ["${digitalocean_ssh_key.default.id}”]
}

resource “digitalocean_loadbalancer “public_lb {
  name = “web-lb”
  region = “lon1”

  forwarding_rule {
    entry_port = 80
    entry_protocol = “http”

    target_port = 80
    target_protocol = “http"
  }

  algorithm = "round_robin"

  droplet_ids = ["${digitalocean_droplet.web.id}”]
} 
```

Enter fullscreen mode Exit fullscreen mode

代码三

根据上面的代码，我们向基础设施添加了一个公共负载平衡器。*名称*和*区域*是不言自明的，它们都是转发规则所必需的。转发规则基本上告诉我们如何发送流量。我们的负载平衡器选择的协议是 HTTP，它的默认端口是 80。`entry_protocol`和`entry_port`简单地陈述了流量是如何被发送到负载均衡器的，`target_port`和`target_protocol`讲述了流量是如何到达连接的微滴的。就这么简单。

这里的下一个重要点是负载平衡器和 droplets 之间的连接算法。在这种情况下，我们选择了循环赛。虽然 Terraform 只支持数字海洋负载均衡器资源的两套算法——循环法(round_robin)和最少连接数(Least _ Connections)——但也存在其他负载均衡算法，如加权循环法、最少流量、源 IP 等。

> 循环法是以某种合理的顺序平等地选择一个组中的所有元素，通常是从列表的顶部到底部，然后从列表的顶部开始，以此类推。认为循环赛的一个简单方法就是“轮流”。作为一个形容词，循环赛变成了“循环赛”。- [什么是](http://whatis.techtarget.com/definition/round-robin)。

在负载平衡器资源块的最后一部分，我们使用`droplet_ids`将我们的 droplets 连接到负载平衡器。我们使用字符串插值来引用液滴 id，这就是 Terraform 如何建立它的依赖图。在 Terraform 中使用插值有一个惯例，它遵循＄{ RESOURCE _ TYPE }的模式。资源名称。属性名称}。在我们的例子中，我们引用了名为`web`的`digitalocean_droplets`资源，并获取了它的所有`id`属性。

通过上面的设置，我们已经能够构建一个简单的第 7 层负载平衡。这里唯一缺少的是数据库服务器。

添加数据库很简单，不像 AWS，到本文发表时，Digital Ocean 还没有提供像 RDS 这样的托管数据库服务。我们将需要推出我们自己的手动。为此，我们将创建一个 droplet 资源，就像我们为 web 所做的那样。代码 VI 中的代码为我们做了同样的事情。

```
resource “digitalocean_droplet “web {
  image = “ubuntu-16-04-x64”
  name = “web-1”
  count = 2
  region = “lon1”
  size = “1gb”
  ssh_keys = ["${digitalocean_ssh_key.default.id}”]
}

resource “digitalocean_droplet “database {
  image = “ubuntu-16-04-x64”
  name = “web-1”
  region = “lon1”
  size = “1gb”
  ssh_keys = ["${digitalocean_ssh_key.default.id}”]
}

resource “digitalocean_loadbalancer “public_lb {
  name = “web-lb”
  region = “lon1”

  forwarding_rule {
    entry_port = 80
    entry_protocol = “http”

    target_port = 80
    target_protocol = “http"
  }

  algorithm = "round_robin"
  droplet_ids = ["${digitalocean_droplet.web.id}”]
} 
```

Enter fullscreen mode Exit fullscreen mode

代码六。

为了让事情进行下去并看到它的效果，我们运行`terraform plan`只是为了确保事情很好并且排序良好，然后我们运行`terraform apply`来构建实际的系统。通过代码 VI 中的设置，我们已经成功地为自己构建了一个简单的基础设施，足以托管一个像样的博客。在本系列的下一部分中，我们将讨论如何在配置之后立即准备我们的机器，并在其上安装基本软件。

*免责声明*

*在遵循本文中列举的例子时，请记住这是有代价的。当你运行`terraform plan`然后`terraform apply`并在你的提供商端创建一个真正的资源时，他们几乎立刻开始向你收费。作为一个警告，这仅适用于非生产环境，总是尝试在你自己之后清洁。为此，Terraform 为我们提供了一个非常方便的命令，叫做`terraform destroy`。`terraform destroy`命令实际上会返回到您的提供商，删除/销毁您创建的每一个资源。这是一个单向命令，无法撤消，所以我强烈建议您在个人或实验环境中这样做。*

*本帖原载于[cyber omin . github . io](http://cyberomin.github.io/engineering/2017/06/24/terraform-part-ii.html)T3】*