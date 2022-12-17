# 在 Amazon EC2 上建立博客

> 原文：<https://dev.to/nathanamorin/setting-up-a-blog-on-amazon-ec2-5ekk>

## 博客简介

[![Setting Up A Blog on Amazon EC2](img/935394f05b98809cb8a3398563b68760.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--J0Y7q5Z---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1484557052118-f32bd25b45b5%3Fixlib%3Drb-0.3.5%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26s%3D524b51959d588b4c8b0abaa0fa17e197)

我开这个博客是为了记录我正在做的一些事情，主要是与技术相关的(汽车修复也可能是特色)。我决定使用 Ghost 博客平台，因为尽管其他平台(Wordpress)有更大的功能集，但我欣赏 Ghost 优先考虑的精简的简单性。最初，我想我会和 Heroku 一起主持(我已经用 Heroku 主持了我的 Node JS 个人网站[nathanmorin.com](https://nathanmorin.com))。然而，我发现 Heroku 并不是 ghost 的理想工具，因为它不能使用标准的 ghost 安装/更新机制。我一直想尝试亚马逊托管有一段时间了&这似乎是一个很好的机会，让我的脚湿在一个简单的项目。我不会在这里进入一个完整的 Ghost 安装教程。Ghost 有一个有用的文档页面(【https://docs.ghost.org/v1/docs/install】T2)。相反，我将通过一些挑战，我发现作为第一次亚马逊 EC2/EFS 用户。

## 建筑

基本架构包括一个 Amazon EC2 实例(在 Cloudflare 后面),数据存储在 Amazon EFS 上(提供资产、图像、文件等的灵活存储)。可以存储在博客上)。

## 流程

虽然亚马逊 AWS 控制台一开始可能会令人生畏，但建立 EC2 Ubuntu 16.04 实例并登录是相当简单的。创建 EFS 存储端点也很简单。然而，通过 NFS4 连接到 EFS 端点有点棘手。在为 EFS 端点创建并添加了正确的安全组之后，Ubuntu EC2 虚拟机上的 NFS 挂载操作将会挂起。在浏览了故障排除文档但没有找到解决方案后，Google 显示我需要将来自我的安全组中的源的入站 NFS 添加到我的安全组策略中。此后，安装变得轻而易举。

## 思想

总的来说，对于以前有管理系统经验的人来说，这个过程相对简单。理解 AWS 的细微差别有一个明确的学习曲线，亚马逊的文档/启动教程肯定可以改进。然而，即使这个项目只是触及了亚马逊大量服务的表面，我也可以欣赏 AWS 提供的配置和架构的组织和灵活性。我将以一些令我印象深刻的事情来结束。设置 EFS，我决定看看我安装的 EFS 驱动器的容量...

```
ubuntu@ip-111-11-11-11:/data/ghost$ df -h  
Filesystem Size Used Avail Use% Mounted on  
udev 488M 0 488M 0% /dev  
tmpfs 100M 4.4M 95M 5% /run  
/dev/xvda1 7.7G 1.9G 5.9G 24% /  
tmpfs 496M 0 496M 0% /dev/shm  
tmpfs 5.0M 0 5.0M 0% /run/lock  
tmpfs 496M 0 496M 0% /sys/fs/cgroup  
tmpfs 100M 0 100M 0% /run/user/1000  
fs-111111.efs.us-east-1.amazonaws.com:/ 8.0E 0 8.0E 0% /data 
```

8eb 可用。这就是自由。