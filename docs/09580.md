# 学习 R

> 原文：<https://dev.to/funkysi1701/learning-r-2p31>

[![R](img/07d3e5d862e3c734829605bdd342bbfa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ie29qYuu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storageaccountblog9f5d.blob.core.windows.net/blazor/wp-content/uploads/2017/02/Exchange-Rate-Calculator.jpg%3Fresize%3D300%252C202%26ssl%3D1) 今天我花了一些时间学习 R 语言。

我试图解决的问题是将一些商品的当地价格转换成欧元。我一直对所有数据使用固定汇率，但由于汇率波动太大，这是不正确的。

我首先想到的是找到一个免费的 API，我可以通过查询来获得我想要的值。我发现的第一个 API 没有覆盖所有的货币，下一个我发现我一次就用完了所有的免费额度。

我的一个同事提到用 R 来解决这个问题，他给我发了一些链接，我开始写我的第一段 R 代码。

我完成的代码可以在 [github](https://github.com/funkysi1701/ExchangeRate/blob/master/script.R) 上找到，我将尝试解释其中的一些。

r 相当简单地定义了函数

> name of function
> {
> 
> arg 1 * arg 2
> 
> }

我创建了一个函数，它有两个参数日期和货币。我知道我有大约 10 种不同的货币，我想得到的货币，我想通过每天循环，所以我需要传递一个日期。

我的汇率信息来源于[www.xe.com](http://www.xe.com)网站，其历史汇率页面将货币和日期传递到查询字符串中，因此我应该能够构建一个包含所有不同元素的字符串。

所有编程语言都可以连接字符串，R 也不例外。它使用 paste()

> var

然而，R 在这个函数中有一个令人讨厌的特性。我希望上面例子中的 var 包含“HelloWorld ”,但它不包含“Hello World”。为什么它会自动加一个我不知道的空格？

> var

我不完全确定所有代码都做了什么，但我可以猜一猜。

我猜 read_html()会加载一个 html 页面，在我的例子中，html_nodes()会查找页面上某个类型的所有 html 标签

, html_table() reads the first table it finds.

table1[2]选择第二列，head()选择具体的行数。我想要第一行和第二列，所以我将这两列合并为 head(table1[2]，1)

现在我找到了我的汇率，我该怎么做呢？r 可以读写 SQL Server，那么为什么不将这些信息存储在 SQL 查找表中呢？然后，在处理数据时，我可以在存储过程中使用这些数据。

要查询 sql，您可以使用 sqlQuery()，它有两个参数，一个 sql 连接和一个 TSQL 命令(例如 SELECT、INSERT、UPDATE 语句)

我使用 while 循环遍历从 2016 年 10 月 1 日到今天的每一天，并查找每种货币的汇率。

现在我手动运行这个 R 脚本，但是有一些方法可以直接从 SQL Server 运行 R，我可能会好好研究一下。然后，我可以有一个 SQL 作业来按计划运行它，也许一天一次，以获取最新的汇率。我还想做一些更聪明的事情，比如通过查询现有的数据库表，只获取我需要的日期的汇率。