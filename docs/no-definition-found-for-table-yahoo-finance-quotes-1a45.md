# 未找到表 yahoo.finance.quotes 的定义

> 原文：<https://dev.to/adamkdean/no-definition-found-for-table-yahoo-finance-quotes-1a45>

我正在写一点代码来抓取股票数据，雅虎的 CSV 数据源是绝对可怕的。CSV 文件中数据中间的逗号？疯狂！

所以我求助于 YQL 唯一真正的另一个来源，没有抓取 html..并且奇怪地发现雅虎的股票数据表不见了。`"No definition found for Table yahoo.finance.quotes"`。其他几个人也发现了这一点，所以经过不到 30 秒的挖掘，我找到了答案:

将`&env=http://datatables.org/alltables.env`添加到您的查询中。

例如:
[http://query.yahooapis.com/v1/public/yql?q=select](http://query.yahooapis.com/v1/public/yql?q=select)*来自 yahoo.finance.quotes 其中 symbol in ("YHOO "、" AAPL "、"谷歌"、" MSFT ")&env =[http://datatables.org/alltables.env](http://datatables.org/alltables.env)

给你。享受吧。