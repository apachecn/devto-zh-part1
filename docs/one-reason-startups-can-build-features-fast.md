# 创业公司能够快速构建功能的一个原因是

> 原文：<https://dev.to/pbeekums/one-reason-startups-can-build-features-fast>

小型创业公司倾向于非常快速地构建功能。尽管大公司拥有更多的资源，但它往往比大公司快得多。为什么会发生这种情况，简单的答案是大公司有很多官僚作风，这拖了他们的后腿。

[![](img/29e6d2844a727f1f84268a1ea8ee4fd1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VSW0Z86I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.professorbeekums.com/img/2017/5/speed.jpg)

然而，事情远不止如此。更大的公司也会有更多的用户。这种单一的差异增加了大公司必须做的大量工作，以构建与小公司相同的功能。

让我们用一个简单的例子，一家初创公司构建了一个待办事项列表应用程序。一个简单的设置是用一个 web 服务器向用户交付 web 页面，用一个数据库在用户的 todo 列表中存储数据。

[![](img/d6bca4c9d1667af19b28a8f67937728d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--CqRaMyT---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.professorbeekums.com/img/2017/5/basicwebapp.png)

一切都会好一阵子。初创公司突突地构建功能，嘲笑他们缓慢移动的竞争。突然间，许多用户开始使用这家初创公司的待办事项应用。他们惊叹于所有的伟大功能！

不幸的是，所有使用这款应用的用户都开始让单个网络服务器超载。一台服务器只能处理这么多的用户流量。如果找不到解决方案，该应用将对所有人关闭。如果没有人能使用这个产品，创业公司就不会持续成功！

解决方案是添加一个负载平衡器。这将简单地在多个 web 服务器之间发送流量。由于它不像 web 服务器做那么多的工作，一个负载平衡器可以处理更多的流量，并将繁重的工作卸载到几十个 web 服务器上。

[![](img/70e6113412069d5ef9a55b1c3936e93c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--s3PcF1Mh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.professorbeekums.com/img/2017/5/loadbalancer.png)

如果创业公司幸运的话，这可能需要几个小时的工作。如果创业公司有大量的技术债务，并且没有人以前安装过负载均衡器，**这可能需要几天或几周的工作。**

哎哟！有很多功能没有建立起来。至少现在情况不错。随着应用程序获得更多的用户，可以创建更多的网络服务器。

不幸的是，这家初创公司遇到了一点小麻烦。这家初创公司令人惊叹的功能之一是在待办事项列表中添加图片。大多数数据库在存储图像方面做得很差，所以这些图像被存储在网络服务器的硬盘上。对于多个 web 服务器来说，这是一个巨大的问题。

负载平衡器需要非常简单。它只是将用户重定向到网络服务器。它不需要花费太多的精力来判断用户使用的是哪个网络服务器。

当一个用户上传一张图片到**网络服务器 1** ，但是第二天他们去查看他们的待办事项列表并且他们在**网络服务器 20** 上会发生什么？

图像不在 Web 服务器 20 上，所以他们看到的只是一个损坏的图像。

[![](img/88b8e2fcc64e46356c19fb88593bd569.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yTjAat3m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.professorbeekums.com/img/2017/5/noexist.png)

天哪！如果我们有 20 台 web 服务器，则有 95%的机会出现映像损坏！我们需要尽快解决这个问题！

幸运的是，有许多解决方案可以处理存储文件。最受欢迎的是亚马逊 S3。当您考虑文件存储时，这似乎是一件非常简单的事情。当你想储存东西时，文件就放进去了。文件会在你需要的时候出现。

再说一次，如果创业公司的技术债务很少，并且有人对这种任务有经验，可能只需要几个小时。也许再花几个小时把所有的图像从网络服务器复制到 S3，或者选择其他存储解决方案。

尽管有很多事情可能会出错。如果初创公司中没有人有文件存储的经验，那么就需要花一些时间来学习。当我们仅仅考虑存储和检索文件时，文件存储似乎很简单。但是权限呢？如果用户有不想让父母或雇主看到的待办事项列表项目的图片，该怎么办？该应用程序已经设置了权限，但现在这些权限也需要在 S3 中进行管理。这又增加了几个小时，可能几天的工作。

文件上传失败的原因也有很多。粗略的手机和 wi-fi 连接经常会导致图像上传失败。大量的 UX、产品和开发工作需要完成，以确保它对人们来说是一个好的体验。用户会原谅失败的上传。他们不太可能原谅上传的“成功”屏幕，只是后来发现它不见了。他们也不喜欢被冻结的网页。

虽然上传失败的经验很重要，但首要任务是从网络服务器上下载所有文件。95%的图像不向用户显示比处理上传失败更重要。不过，处理好故障会增加以后几天/几周的工作量。

我们不要忘记[技术债](https://blog.professorbeekums.com/2016/06/technical-debt.html)可能出现的问题。很有可能在图像功能的开发中没有太多的前瞻性思维。否则的话，创业公司就永远不会首先处理文件存储在 web 服务器上的问题。这意味着这个特性也有很多技术上的问题。

一种可能性是图像的完整路径存储在数据库中。示例:待办事项列表项“购买牛奶”有一个到“[https://awesometodolist.cimg/user/john/milk.png"](https://awesometodolist.cimg/user/john/milk.png%E2%80%9D)的链接。

好了，现在必须去 https://awesometodolist.s3.amazon.com/user/john/milk.png"使用 S3。我们必须检查数据库中的每个待办事项列表项，并更新这些 URL。

像这样的[数据迁移](https://blog.professorbeekums.com/2016/10/how-to-handle-data-migration.html)有很多复杂性，但是由于我们处于 95%的图像都无法加载*的情况下*，这里的工作可以相当简单。不过，这仍需要几天的时间，因为初创公司需要确保不会搞砸。这是不容出错的事情之一。

**创业公司为什么需要这么小心？怎么会比 95%的图片加载失败更糟糕呢？**

目前，启动至少知道属于待办事项列表项的文件名。这意味着总是有机会正确检索这些文件。

[![](img/2f4c81ef87d2c8eb1d6680503e248068.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--q0eev4Bp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.professorbeekums.com/img/2017/5/findimage.png)

如果迁移工作出错，文件名被意外地从待办事项列表项中删除，我们将永久丢失数据。强调永久。无论初创公司做什么，这些数据都永远无法恢复。图像在 S3 的某个地方并不重要，初创公司无法知道它与哪个待办事项列表项相关联。

[![](img/f530778a4317eed2a6baf58af7bf7193.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TuQJQWDM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.professorbeekums.com/img/2017/5/noimage.png)

所以这几天的工作主要是测试，以确保没有类似的错误。

可能出现的另一种形式的技术债务是草率的代码。好的代码应该包含存储和检索图像文件的单一位置。 [![](img/ee0de4264dd9fe5d19399c09ed65ddd6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--akSXrMpA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.professorbeekums.com/img/2017/5/goodcode.png) 不幸的是，这家初创公司的代码并没有那么伟大。存储和检索图像文件的代码已经被复制和粘贴到几十个，可能是几百个地方。 [![](img/afa4f41a278c6db911e33cc50b026fe8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jtlbW1MZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.professorbeekums.com/img/2017/5/sloppycode.png) 进行搜索和替换听起来很容易，但是代码中的细微差别可能会存在并导致问题。每个变化都需要测试，当问题出现时需要解决。

这家初创公司或许可以修复用户最常用的少数功能。这应该只需要几个小时。但整个系统迟早需要修复，这可能需要数周的时间。

唉。这么多时间没有花在构建功能上！

不幸的是，创业公司的麻烦才刚刚开始。[下一篇文章](https://blog.professorbeekums.com/building-no-features-2)继续这家初创公司的成功之旅。

*本帖最初发表于[blog.professorbeekums.com](https://blog.professorbeekums.com/building-no-features-1/)T3】*