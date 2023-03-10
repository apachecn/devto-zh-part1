# 黑火-马根托公司

> 原文：<https://dev.to/ajardin/blackfire-magento-475j>

我用 Blackfire 分析的第一个东西是一个使用 PHP 5.5 的 Magento 项目。像大多数不断添加新功能的现有项目一样，随着代码库的不断增长，我们开始越来越频繁地面临性能问题。该项目始于四年前。

> 本文使用了几个与 Blackfire 相关的术语。如果你不熟悉，建议你看一下讲解的优秀的 [PHP 代码性能。](https://blackfire.io/docs/book/index)

## 暗访

Blackfire 提供了几个统计数据:墙时间、I/O 等待、CPU 时间、内存消耗、网络、HTTP 和 SQL 查询。这是我的第一个配置文件，没有页面缓存和块缓存。它允许分析真实的输出，而不是整个 Magento 初始化。

[![Blackfire results before optimizations](img/5bfdd50dceaa357fd568fbc2c7e0fdd4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hfjLhkS5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ajardin.fr/public/img/screenshots/profile_before_toolbar.png)
[![SQL queries before optimizations](img/549a18faa8e8ac02aa0dbfc1268d51a5.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--nMIzaaTO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ajardin.fr/public/img/screenshots/profile_before_sql.png)

您已经可以看到，仅在与 URL 重写系统相关的内容上，每页就执行了 700 多个 SQL 查询。同时，调用图显示`navigation/top.phtml`模板内的**包含时间**等于**3.92 秒**。

经过进一步的调查，我发现性能问题是由于在主菜单生成中错误地使用了自定义方法。在我们检索每个类别 URL 来填充 HTML 链接的地方，这个定制方法从数据库系统地加载它。

在我们的例子中，`url_path`属性总是完全等于规范的 URL。默认情况下，该属性已经加载到 categories 集合中。因此，修复非常简单:通过 Magento getter 访问属性。