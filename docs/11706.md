# 介绍 iDeal.NET

> 原文：<https://dev.to/robinvanderknaap/presenting-ideal-net-41ho>

iDeal 是荷兰领先的在线支付平台。去年，在开发 bidfortickets.nl 时，我不得不创建一个 API 来与我们的理想提供商(Rabobank)进行交互，以处理所有的在线支付。我注意到有很多例子存在，特别是对于 PHP，但是没有什么是真正有用的。网络生态系统。所以我决定在 GitHub 上开源 API，并将其命名为 iDeal.NET。该 API 负责与理想提供者的所有通信，并且很容易集成到. NET (web)应用程序中。

### 支持的理想版本

iDeal.NET 的目标是理想的专业银行(Rabobank)、理想的 Zelfbouw (ABN Amro)、理想的综合银行和理想的高级银行(ING Bank)。这些版本允许对交易进行实时反馈。

iDeal.NET 还不支持 iDeal Basic (ING Bank)、iDeal Hosted、iDeal Lite (Rabobank)和 iDeal Zakelijk，这些软件很容易在应用程序中实现，但不允许对交易进行实时反馈。

### 来源、样本和文档

源代码存放在 GitHub 上，您也可以在这里找到文档。该解决方案包括一个样例 ASP.NET MVC 应用程序，它展示了 iDeal.NET 的基本用法。示例应用程序使用 [www.ideal-simulator.nl](http://www.ideal-simulator.nl/) 来模拟支付和检索支付状态的整个过程。这个例子的真实版本可以在[这里](http://ideal.skaele.it/)找到。

### 用法

iDeal 的实现基本上是关于三种类型的请求:目录请求、事务请求和状态请求。iDeal.NET 提供了一个易于使用的 API 来发送请求和处理响应。

例如，检索银行(发行者)列表的目录请求非常简单: