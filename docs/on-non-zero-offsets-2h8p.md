# 关于非零偏移

> 原文：<https://dev.to/jacoby/on-non-zero-offsets-2h8p>

这是一个我不得不写的话题，但我知道这不是我期望任何人阅读的东西。`Blog`来源于`weblog`,来源于人们在网络上记录他们的行为。

它来自 Globus，这是一种服务，我说*“我想与 Joe 分享大量数据”*，Globus 让我的服务器知道这是 Joe，Joe 就是他所说的那个人，如果 Joe 的计算机和我的计算机之间出现问题，它会尝试从我们停止的地方继续。

我们从一个叫 GridFTP 的协议中得到这个。我只通过 Globus 使用过它，所以除此之外我不能说太多。但它的一个特点是偏移。如果我用`get(big_file.tar)`开始传输，那是从头开始，但是用`get(big_file.tar, 12345678)`，它开始传输大约 10 亿字节。

我认识的 Joe 在校园的另一边工作，他可能想通过快速的校园主干网将文件移动到他办公桌旁的千兆网络接入点。但乔可能在别的地方，跨越几个网络跳转和政治边界。乔是一个非常受欢迎的名字。

再举一个例子，它可能不是一个 16GB 以下的小文件目录(我们现在生活在一个 16GB 很小的世界里)，而是一个 300 GB 或更多的大文件包。如果我试图通过一个不太好的连接，一个可能显示出脆弱迹象的连接，这将是一件非常好的事情。

但是，长期存储计算机，你在那里存储 tarballs 拥有很多人的档案。它实际上可能有以`tar`–`*t*ape *ar*chive`命名的磁带。因为它应该是稳定的，它不可能运行最新的软件，允许你在 12345678 位重新开始下载的代码可能不存在。

但那会是你想要的地方。能够在 300GB 下载中途重启比 30GB 下载重要得多。

理论上。

不打算或不应推断与实际系统(活着的或死去的)的认同。在撰写这篇博文的过程中，没有人受到伤害。

如果你有任何问题或意见，我将很高兴听到它。在 [Twitter](https://twitter.com/jacobydave) 或[上问我，在我的博客回购](https://github.com/jacoby/jacoby.github.io)上做文章。