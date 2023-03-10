# 用 Javascript 构建比特币计算器

> 原文：<https://dev.to/cizu64/building-a-bitcoin-calculator-with-javascript-1jjc>

# 简介

嘿，伙计们，我将向你们展示如何使用比特币汇率和支持所有主要货币的货币转换。我目前正在使用 Coindesk，它提供了 API，可以让其他人以编程方式使用数据。

要使用 API，不需要注册，是免费的！

你可以在他们的网站上查看支持的货币([http://coindesk.com](http://coindesk.com))。要使用端点，您需要请求:

[https://API . coin desk . com/v1/BPI/current price/[code]。json](https://api.coindesk.com/v1/bpi/currentprice/%5Bcode%5D.json)

code 参数接受有效的 ISO 4217 货币代码

## 一个样本请求

[![sample](img/641a31a07a48fc665e05abf5bb6af7f1.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--C3Q690bC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://3.bp.blogspot.com/-57hNfi3JTYE/Wl9ubE5FpFI/AAAAAAAAAfM/bKnAXzbWLVQhMViDXs-FiDKxsZNr55X6wCLcBGAs/s1600/Untitled.png)

看一下结果，您会注意到 rate 和 rate_float 对象，其中包含当地货币对 BTC 的汇率。下面是一个将 BTC 兑换成当地货币的公式。

###### 公式= BTC 金额*汇率=当地货币价格

# 构建比特币计算器

首先要做的是使用客户机来消费从端点返回的 JSON 数据。你可以使用 Asp.Net http client 类，Jquery getJson()函数，我们将在这里使用，或者你可以使用其他你已经知道的。

```
$.getJSON( "https://api.coindesk.com/v1/bpi/currentprice/usd.json", function( data) {
   var amountInBtc = 0.005; //convert 0.005 btc to usd
   var exchangeRate = parseInt(data.bpi.USD.rate_float);
   var amount = amountInBtc * exchangeRate;
   console.log(amount);
}); 
```

Enter fullscreen mode Exit fullscreen mode

跟随我:

博客:[https://techcerberus.blogspot.com](https://techcerberus.blogspot.com)
推特:[https://twitter.com/charlesnnaji](https://twitter.com/charlesnnaji)T5】中:[https://medium.com/@cizu64](https://medium.com/@cizu64)