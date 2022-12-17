# 尝试学习围棋-构建下载器第 1 部分

> 原文：<https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-01-44gl>

在过去的几个月里，我一直在玩围棋，并且乐在其中。现在我有了一点空闲时间，是时候加倍努力加强我的技能了。我也想养成多写的习惯，这是一个绝佳的机会。所以，在接下来的几篇文章中，让我们一起来黑一个小服务器吧。

## 想法

我遇到的最大的问题之一是试图想出一些事情来做。几天前我想开始这个系列，但无法决定要建立什么。它需要足够小，我可以在几个小时内完成骨架。然而，它需要足够复杂，以帮助我接触更多的语言。我想了想，决定设计一个简单的服务器，从远程服务器下载文件。这个想法有一些实际的来源，有时我会看到一些我可能需要的东西。一些我现在不需要的东西，但是如果需要的话，我可以将它们存档并在以后使用。如果我在一个网页上，那么我想右击并选择一个菜单选项，而不必马上再考虑它。就像 Dropbox 一样，我可以把一个文件 URL 扔给它，让它处理所有其他的事情。

当然，这看起来有点过头了(很可能是这样)，但是，我需要一个项目来做...

## 高等级

我们的下载器将是一个简单的 web 应用程序。在项目接近尾声时，我将研究使用 Let's Encrypt 从应用程序为 HTTPS 提供服务。由于这是一种存档，服务器将生活在场外。为了测试，谷歌计算引擎的一个微型实例将会工作。我打算尽可能长时间地只使用 Go [标准库](https://golang.org/pkg/#stdlib)。这毕竟是一次学习练习。

首先，服务器本身有两个端点:

`http://<server>/`

*   GET / -简单的服务器状态，正常运行时间大概。

`http://<server>/download`

*   上传/下载——接受一个包含下载细节的 JSON 对象

## JSON 对象

```
{  "title":  "Name of download (filename probably)",  "location":  "URL of download"  } 
```

服务器将接收 JSON，解析它，然后发出 GET 请求来下载文件。检索后，我们将文件保存到磁盘。现在，让我们先解释一下 JSON 对象是如何创建的。我们会在某个时候回到这个话题。有了一个基本的想法，我将写一些代码，并尝试记录我在每个小 sprint 中所做的事情。

直到明天...

第二部分已经发布。

* * *

你可以在 GitHub 上的 repo 中找到这个和大多数其他试图学习围棋的帖子的代码。

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [【新达昆】](https://github.com/shindakun) / [ atlg](https://github.com/shindakun/atlg)

### 我在 dev.to 上发布的“尝试学习围棋”帖子的来源报告

<article class="markdown-body entry-content p-5" itemprop="text">

# 尝试学习围棋

在这里你可以找到我为尝试学习围棋而写的代码，这些代码是我在 [Dev.to](https://dev.to/shindakun) 上写的。

## 帖子索引

| 邮政 | 密码 |
| --- | --- |
| [制作下载器第 01 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-01-44gl) | - |
| [制作下载器第 02 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-02-2k7i) | - |
| [制作下载器第 03 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-03-2214) | - |
| [制作下载器第 4 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-04-3ln9) | - |
| [建造下载器第 05 部分](https://dev.to/shindakun/attempting-to-learn-go---building-a-downloader-part-05-44o) | - |
| [使用 REST API](https://dev.to/shindakun/attempting-to-learn-go---consuming-a-rest-api-5c7g) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-01/main.go) |
| [继续休息冒险](https://dev.to/shindakun/attempting-to-learn-go---continuing-rest-adventures-2l4l) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-02/main.go) |
| [现在发送休息请求](https://dev.to/shindakun/attempting-to-learn-go---now-sending-rest-requests-akp) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-03/main.go) |
| [REST API 和模板上的位](https://dev.to/shindakun/attempting-to-learn-go---rest-api-and-a-bit-on-templates-4kca) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-04/main.go) |
| [再次通过 API 发送电子邮件](https://dev.to/shindakun/attempting-to-learn-go---sending-email-via-api-again-2e4e) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-api-05/main.go) |
| [让我们模块化吧！](https://dev.to/shindakun/attempting-to-learn-go---lets-get-modular-390i) | [src](https://github.com/shindakun/mailgunner) |
| [让我们再一次模块化！](https://dev.to/shindakun/attempting-to-learn-go---lets-get-modular---again-10cd) | [src](https://github.com/shindakun/mailgunner) |
| [构建开发日志第 1 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-01-1c3m) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-01/main.go) |
| [构建开发日志第 2 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-02-179c) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-02/main.go) |
| [构建开发日志第 3 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-03-7lk) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-03/main.go) |
| [构建开发日志第 4 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-04-2bok) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-04/main.go) |
| [构建开发日志第 5 部分](https://dev.to/shindakun/attempting-to-learn-go---building-dev-log-part-05-4mo1) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devsite-05/main.go) |
| [按扩展名 01 列出文件](https://dev.to/shindakun/attempting-to-learn-go---listing-files-by-extension-1n10) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-sort-01/main.go) |
| [按扩展名 02 列出文件](https://dev.to/shindakun/attempting-to-learn-go---sorting-and-moving-files-by-extension-227j) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-sort-01/main.go) |
| [开发至 API 01](https://dev.to/shindakun/interacting-with-the-devto-article-api-4g34) | [src](https://raw.githubusercontent.com/shindakun/atlg/master//go-devtoapi-01/main.go) |
| [开发至 API 02](https://dev.to/shindakun/interacting-with-the-devto-article-api---again-sort-of-2o8g) | 参见上面的代码 |

</article>

[View on GitHub](https://github.com/shindakun/atlg)

* * *

| 喜欢这篇文章吗？ |
| --- |
| 给我买杯咖啡怎么样？ |

* * *