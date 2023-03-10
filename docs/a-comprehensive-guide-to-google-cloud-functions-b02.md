# 谷歌云功能综合指南

> 原文：<https://dev.to/divyenduz/a-comprehensive-guide-to-google-cloud-functions-b02>

### 简介

[云功能](https://cloud.google.com/functions/)允许我们在不提供任何基础设施的情况下编写业务逻辑。在本帖中，我们将讨论可供我们使用的[云功能](https://cloud.google.com/functions/)的风格、局限性、开发/部署工作流程、使用云功能模拟器和代码的本地开发。

谷歌对[云功能](https://cloud.google.com/functions/)的描述如下:—

> Google Cloud Functions 是一个轻量级、基于事件的异步计算解决方案，它允许您创建小型、单一用途的函数来响应云事件，而无需管理服务器或运行时环境

事不宜迟，我们开始吧。

### 云函数运行时和依赖关系

[云函数](https://cloud.google.com/functions/)是用 Javascript 编写的模块，运行在[节点运行时](https://nodejs.org/en/)。[云函数](https://cloud.google.com/functions/)执行环境遵循[节点](https://nodejs.org/en/)“LTS”版本，当前[云函数](https://cloud.google.com/functions/)运行的节点版本为[节点](https://nodejs.org/en/) v6.11.1

对于依赖性，只有好消息，因为您可以使用 [npm 和 package.json](https://cloud.google.com/functions/docs/writing/dependencies) 的强大功能

当从本地机器部署时，正如我们将在这篇文章中进一步看到的，您可以部署配置文件/凭证密钥进行访问(尽管不建议对凭证密钥进行版本控制)。使用[Google Cloud Deployment Manager](https://cloud.google.com/deployment-manager/)进行供应配置。

### 云函数的类型

谷歌云功能有两种风格:-

#### 前台(HTTP)功能

这些函数获取一个 HTTP URL，以便从外部调用。比如下面这个，看看吧。

[https://us-central 1-divu-178107 . cloud functions . net/hello-world](https://us-central1-divu-178107.cloudfunctions.net/hello-world)

编写一个 HTTP 云函数非常简单，您可以导出一个带有请求、响应参数的函数(比如 express)。以上函数的代码如下所示:-