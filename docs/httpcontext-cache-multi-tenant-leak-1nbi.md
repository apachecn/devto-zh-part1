# HttpContext。缓存多租户泄漏

> 原文：<https://dev.to/collinbarrett/httpcontext-cache-multi-tenant-leak-1nbi>

[![CPU in Socket](img/4b7cf2d35b6b036baaa45a3066725606.png)T2】](https://collinmbarrett.com/httpcontext-cache-multitenant-leak/)

## 调用缓存

我支持的[产品](https://collinmbarrett.com/joining-shelby-systems/)在几个页面上都有图表小部件。我们需要重构他们的一些低效查询，但我们最近面临着实现快速[性能](https://collinmbarrett.com/tag/performance/)提升的压力。我的团队试图安装[缓存](https://collinmbarrett.com/tag/cache/)，这样图表数据集每天只被检索一次。

## 漏洞百出的方法

一个同事用了 ASP.NET 的 HttpContext。缓存将数据集保存在内存中。该解决方案修剪部件名称作为关键，并降低了重复访问的页面加载速度。

发布后，我们收到了一些托管在共享服务器上的客户的报告。在少数情况下，用户看到了他们不认识的图表数据。他们可能看到了其他客户缓存的数据。

> 每个[应用程序域](https://docs.microsoft.com/en-us/dotnet/framework/app-domains/application-domains)都有一个缓存类的实例。因此，由 Cache 属性返回的 Cache 对象是应用程序域中所有请求的 Cache 对象。
> 
> ——<cite>——[——](https://docs.microsoft.com/en-us/dotnet/api/system.web.httpcontext.cache)——</cite>

因为这个键对于客户来说不是唯一的，所以我们的环境在整个应用程序域中共享缓存的数据。我们误解了 HttpContext。缓存的范围改为单个应用程序实例。

## 分辨率

大部分泄露的数据都是一般/概要信息，并不高度敏感。我们在不到 24 小时内部署了一个修复程序来禁用该功能。

一个简单的解决方法是在键中使用一段特定于客户的数据来保持数据集的唯一性。从长远来看，我们应该将每个实例放在一个隔离的容器中，但是我们还不能支持这一点。

## QA 提醒

在我们隔离每个实例之前，QA 应该测试多租户环境中的所有更改。

*<small>免责声明:所表达的观点仅是我个人的观点，不代表我的雇主的观点或意见。</small>T3】*