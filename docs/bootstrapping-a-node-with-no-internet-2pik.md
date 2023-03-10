# 引导没有互联网的节点

> 原文：<https://dev.to/thaiwood/bootstrapping-a-node-with-no-internet-2pik>

当您使用`knife bootstrap`引导一个节点时，Chef 假设您可以访问互联网。它使用这个来下载客户端包和一些元数据，但是您不必连接到互联网来引导节点。

这在你的防火墙系统不允许你从互联网上获取软件包的情况下尤其方便。

有两种方法可以解决这个问题。首先是利用`--bootstrap-install-command`标志。

## 对于使用`apt`的发行版

```
$ knife bootstrap chefnode -N MyNewNode --bootstrap-install-command "curl http://your-internal-server/chef.deb -o /tmp/chef.deb && dpkg -i /tmp/chef.deb 
```

Enter fullscreen mode Exit fullscreen mode

## 对于使用`yum`的发行版

```
$ knife bootstrap chefnode -N MyNewNode --bootstrap-install-command "yum install -y http://your-internal-server/chef.rpm" 
```

Enter fullscreen mode Exit fullscreen mode

这种方法对于一次性的或者数量很少的机器来说是一个很好的选择，但是如果你有更多的机器，那么更好的选择是制作一个引导模板。

## 制作自己的引导模板

引导模板只是 Chef 用来确定如何引导节点的`erb`文件。您可以用`knife`标志`--bootstrap-template`覆盖默认设置

你可以制作自己的模板，放在`~/chef-repo/.chef/bootstrap`(可能要制作`bootstrap`目录)。

制作自己的模板最简单的方法是从默认的 [Chef 模板](https://github.com/chef/chef/blob/master/lib/chef/knife/bootstrap/templates/chef-full.erb)开始，修改它以包含您需要的引导命令，类似于上面的。

一旦您创建并保存了自己的模板，现在您可以将您的命令改为(假设您创建了一个`debian.erb` ):

```
$ knife bootstrap chefnode -N MyNewNode --bootstrap-template debian 
```

Enter fullscreen mode Exit fullscreen mode

你可能已经注意到，这两个方法在功能上是一样的，传入命令，它被插入到模板中，制作一个模板，把你的命令放进去，结果一样。我建议您对多个节点使用模板的原因是，您可以将引导文件保持在版本控制中，尽管这需要更多的步骤。

### 版本控制你的引导文件

由于`knife`将在几个地方寻找一个`.chef/bootstrap`目录，我们必须以某种方式将我们的引导文件保存在那里。问题是我们不应该将`.chef`目录提交给版本控制，因为该目录包含键。相反，你可以做的是创建一个包含你的文件的`~/chef-repo/bootstrap`文件夹，而不是像我们上面做的那样创建目录，相反，我们会符号链接它。

从您的`~/chef-repo` :

```
$ ln -s ../bootstrap .chef/bootstrap 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以将您的`~/chef-repo/bootstrap`目录添加到您的下一次提交中，而无需暴露密钥或跟踪一堆引导命令。

你怎么想呢?留下评论。点击这里，如果你想看到更多这样的:[https://ThaiWood.IO/DevTo](https://ThaiWood.IO/DevTo)