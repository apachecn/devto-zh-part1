# 查找数据包

> 原文：<https://dev.to/sroy8091/finding-packets>

# 我的要求在哪里？

当在浏览器中键入 URL 时，它会检查 IP 地址的 DNS 映射是否在缓存中。如果存在，那么它向服务器请求文档，否则操作系统对 DNS 解析器进行递归查询，并将域名转换为 IP。你可以通过这个[链接](https://dev.tochrome://net-internals/#dns)来检查你的 chrome DNS 缓存。这是我的截图。

[![alt text](img/68958d24e76a03a9c04ffea2d491dbb8.png "chrome dns cache")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MR2wS0MA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/870nxvpjthittjjr3yif.png)

在通过任一种方式解析 IP 后，我们的浏览器表现得像一个客户端，请求该 IP 上的服务器提供文档。我们也可以使用 telnet 来请求服务器。是的，我就是这么做的。

# 寻找数据包

我在中请求我们的大学服务器，即 [https://www.kgec.edu .并得到以下结果。](https://www.kgec.edu.in)

[![alt text](img/5304854b85da2ad72e665da2bd3f97cf.png "finding packets")T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--RFxLF671--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xexvhti3zo1vrrk3agb9.png)

所以我的请求是通过这条将近 1410 英里的路线。这让我了解到，通过这个了不起的人发明的“互联网”，我们获取世界信息的速度有多快。

为了浏览我为此写的代码，转到这个[链接](https://github.com/sroy8091/request-tracer)。

*这里是关于这一系列 snipcode 开发的所有帖子的索引*

[第一部分:App Idea](https://dev.to/sroy8091/app-idea-for-hasura-internship)
[第二部分:App 原型](https://dev.to/sroy8091/app-prototype)
[第三部分:本地开发](https://dev.to/sroy8091/hasura-local-development)
[第四部分:G for Git](https://dev.to/sroy8091/g-for-git)
[第五部分:数据建模](https://dev.to/sroy8091/its-modeling-time)
[第六部分:Data&Auth API](https://dev.to/sroy8091/postman-a-major-tool-for-developers)
[第七部分:基本功能](https://dev.to/sroy8091/i-love-cookies-expressjscookies)
[第八部分:App 屏幕 1](https://dev.to/sroy8091/i-built-it-with-codemirror)
[第九部分](https://dev.to/sroy8091/app-screen-2)

| 喜欢我的帖子吗？ |
| --- |
| [![](img/f4f06ea384c5e485d3474b718bbe6f95.png)T2】](https://ko-fi.com/A0A5WBC1) |