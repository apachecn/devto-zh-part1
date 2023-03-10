# 自动化我的服务器设置的好处

> 原文：<https://dev.to/domysee/the-bliss-of-automating-my-server-setup-9d9>

我有一个 VPS，上面有我的一些兼职项目。最近要设置几次，因为我把它弄得不能用了(请不要问我怎么弄的，真的很蠢)。

每次发生这种情况，我都要花几个小时重新设置。当然，我对维护服务器了解不多，你可能比我做得快得多，但你永远不会比脚本快。所以，在第二次之后，我终于决定是时候自动化这个过程了。

## 目标

我在主持 ElasticSearch 和一些。Net 核心应用程序。它们都可以通过 nginx 反向代理访问。

另外，还有几个 cronjobs。

在写脚本的时候，我决定在我所有的。Net 核心应用程序，这样就少了一个需要安装的东西。

这是极其容易的 btw，只需右键- >添加 Docker 支持- >就搞定了。但是我跑题了。

该脚本应该

*   安装坞站、坞站-合成和 nginx
*   提取所有 Docker 图像
*   将 nginx 和 ElasticSearch 配置文件复制到正确的位置
*   添加 cronjobs
*   启动所有应用程序
*   配置服务器，使应用程序在重新启动后自动启动
*   安装我喜欢的其他应用程序(例如 htop)

## 脚本

编写脚本很简单，比我想象的要简单得多。它基本上是复制和粘贴来自供应商网站的命令，并使用 Docker CLI，这是非常好的记录。

坞站:https://docs . docker . com/engine/installation/Linux/docker-ce/Ubuntu/# set-up-repository

docker-compose:https://docs . docker . com/compose/install/

engine:t8

不过，我在使用特定的软件时确实遇到了一些小困难，这将在以下两个部分进行描述。

### 弹性搜索

ElasticSearch 有个小谬误。默认情况下，`vm.max_map_count`环境变量被设置为`65536`。

这对于 ES 来说太低了，导致内存不足异常。这是非常混乱的，如果有几个 GB 的空闲内存。

解决方案是将`vm.max_map_count`设置为至少`262144`，正如[文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/vm-max-map-count.html)中提到的，这在一些谷歌搜索后很快就能找到。

### Cronjobs

遗憾的是，`crontab`没有提供简单添加条目的方法。但是不出所料，其他人也有同样的问题，Stackoverflow 有一个简单易用的解决方案。

```
(crontab -l 2>/dev/null; echo "@reboot nginx -c /home/ubuntu/nginx/nginx.conf") | crontab - 
```

Enter fullscreen mode Exit fullscreen mode

这还演示了服务器重启后应用程序是如何启动的。简单地用一个 [`@reboot`](https://www.digitalocean.com/community/tutorials/how-to-use-cron-to-automate-tasks-on-a-vps) crontab 条目。

## 结论

设置过程的自动化将我的服务器准备就绪的时间从几个小时缩短到了几分钟。在那幸福的几分钟里，我什么都不用做，只是看着剧本工作。

此外，我觉得做实验更舒服。如果有什么东西坏了，我只需要启动脚本。

自动化很棒！

[![Automate all the Things](img/8bccf47d22457f39b5403f29b0c3a943.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rIxK3pLR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.device42.com/blog/wp-content/uploads/2016/04/aatt.jpeg)

PS:如果你对整个剧本感兴趣，可以在这里找到[，当然减去我的凭据；)](https://domysee.com/pages/server_setup_script/)

* * *

在 Twitter 上关注我，了解更多我的想法、文章、项目和工作。