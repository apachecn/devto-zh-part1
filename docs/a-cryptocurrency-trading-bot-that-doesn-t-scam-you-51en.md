# 不会骗你的加密货币交易机器人

> 原文：<https://dev.to/saschb2b/a-cryptocurrency-trading-bot-that-doesn-t-scam-you-51en>

[![](img/f990a5656b7dfd907d9e48f5c2bbc2ed.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--gSIDXh3a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/780/0%2ArslltOp4vWpMGgUS.png)

加密货币交易机器人正在成为人们认为他们需要的下一个大东西。所以他们买了一个。我也是。

> 这不是一个复制&意大利面的例子。它更像是如何实现你想要的目标的指南

### 第 0 步:学会艰难地扔掉钱

投资第三方加密货币交易机器人让你觉得你做了正确的事情。你可以什么都不做，最终变得富有。机器人会为你赚钱，对吗？不对！

你只是送钱买这个机器人让开发商发财。他什么也不做，除了到处更新。因此，这样一来，他确实可以通过加密货币机器人赚钱。

但是你仍然不富有。该死的。怎么办？

### 第一步:自己写

您应该信任的唯一机器人来自您构建的代码库。它来自于你发明的一种逻辑和模式。不要让任何人告诉你什么是对的。

我编了一个，你也可以！所以让我们开始吧。(剧透:我还不富裕)

如果你愿意，你也可以克隆我的 github repo。

[https://github.com/TeamWertarbyte/crypto-trading-bot](https://github.com/TeamWertarbyte/crypto-trading-bot)

### 第二步:找一个好的加密货币交易市场

这是最难的部分。为你的机器人找到一个交易的好地方。好的起点要么是 bittrex 要么是 kraken。两者都有很好的 API 可以使用。

[https://bittrex.com/Home/Api](https://bittrex.com/Home/Api)

[北海巨妖|购买、出售和保证金交易比特币(BTC)和以太坊(ETH) - API](https://www.kraken.com/help/api)

创建一个帐户，让我们进入下一步。

### 第三步:定义 bot 环境

对于我的机器人，我坚持使用 bittrex.com，它有一个可靠的 API 和良好的响应时间。他们还为实时更新提供了一个 websocket。

我在这个例子中使用的框架是 node js。但是你可以选择你喜欢的。我想让它在命令行中通过 docker-compose 运行。所以 node js 是个不错的选择。

可以通过 npm 包访问 API，如

[dparlevliet/node . bittrex . API](https://github.com/dparlevliet/node.bittrex.api)

或者编写自己的 REST 客户端来访问 bittrex。bittrex 中 API 的文档已经相当过时了。它是针对 1.1 版本的，这个版本已经使用了一段时间了。Bittrex 还接受尚未正式发布的 2.0 版本的呼叫。上面提到的 node 包很好地实现了闪亮的新 2.0 调用。用那个！

### 第四步:定义你的交易逻辑

当你想处理这些数字时，你需要一些市场指标。对我来说，这是 EMA(指数移动平均线)值。你可以在这里阅读更多关于其他价值观的信息:

[市场指标【图表学校】](http://stockcharts.com/school/doku.php?id=chart_school:market_indicators)

[![](img/84a48a511c51ee2cfd8206606fe40d73.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--DnSFH0Qx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ApmFIYVLaKWWUAgqrc-aOtw.png)

我选择了两个小时的市场指标。

*   (红色)周期为 50 的均线
*   (蓝色)周期为 15 的均线

单独使用它们并不十分有效。但是看看它们交叉的那些点。它们是买卖订单的理想进场点或出场点。

#### 所以我的交易逻辑很清晰

如果均线 15 大于均线 50 就买入，如果均线 50 大于均线 15 就卖出。这样，我的钱总是在上涨的替代币中，一旦下跌，就卖回比特币。

这只是无数组合中的一种。尝试最适合你的。我正在研究一种人工智能，它可以检测最佳市场指标，并创造新的指标，但这将是另一篇文章。

### 第五步:Docker

你的机器人必须全天候运行，否则你将无法盈利。所以我把我的机器人包装在一个永远运行的 docker compose 中。

Dockerfile:

```
FROM node:latest

# Create app directory
RUN mkdir - p / usr / src / app
WORKDIR / usr / src / app

# Install app dependencies
COPY package.json / usr / src / app/  
RUN npm install

# Bundle app source
COPY. / usr / src / app

RUN npm run build
CMD ["npm", "start"] 
```

坞站-化合物. yml〔t0〕

```
version:'2'
services:  
 server:  
 build:.
restart:'unless-stopped' 
```

### 第六步:循环

让我们定义循环及其配置。我会一步一步解释每个循环动作，所以要有耐心。我们的配置对象将随着时间的推移而增长。我们的构造函数创建了三个内部变量。

*   bittrex—bittrex API 的接入点
*   货币——保存我们的人口币，它的价值，市场指标和所有其他与币相关的东西
*   比特币——只是从货币中提取出来的比特币

因为并发控制，我用蓝鸟作为我的 promise 库。

```
const now = require('performance-now')
const Promise = require('bluebird')
const log = require('fancy-log')

Promise.config({
 cancellation: true  
})

const configs = {
 cancelOpenOrdersOnStart: true,
 refreshTimeout: 180000 // 3 minutes
}

const INVEST\_HOLD\_OR\_REJECT = {
 HOLD: 'HOLD',
 INVEST: 'INVEST',
 REJECT: 'REJECT',
 NONE: 'NONE'
}

export default class Watcher {
 constructor (bittrex) {
this.bittrex = bittrex
this.currencies = []
this.bitcoin = {}
 }

async watch () {
const start = now()
 log.info(`## Started emma watch ##`)

 configs.cancelOpenOrdersOnStart && await this.cancelOldOrders()

await this.fetchMarkets()
await this.fetchBalances()

await this.injectMarketSummaries( this.currencies)
await this.filterCurrenciesByMarkets(configs.markets)
await this.injectCandleData( this.currencies)
await this.injectMarketIndicators( this.currencies)
await this.countEmaCrossPointTicks( this.currencies)
await this.injectClosedOrderHistory( this.currencies)

await this.injectMarketSummaries( this.currencies)
await this.evaluateInvestHoldOrReject( this.currencies)

await this.rejectBadInvestments( this.currencies)
await this.invest( this.currencies)

 log.info(`## Finished ${(now() - start).toFixed(5)} ms ##`)
 setTimeout(() =\> this.watch(), configs.refreshTimeout)
 }
} 
```

#### 第 6.1 步:取消旧订单

我们的机器人下的订单可能不总是能通过。所以如果旧订单不被接受，我们不得不取消。

```
async cancelOldOrders () {
const start = now()
const openOrders = await this.bittrex.getOpenOrders()

if (openOrders.length \> 0) {
for ( let openOrder of openOrders) {
const elapsedHours = ( new Date() - new Date(openOrder.Opened)) / 1000 / 60 / 60
if (elapsedHours \> configs.maxHoursToHoldOrder) {
 log.info(`Cancel ${openOrder.OrderType} on ${openOrder.Exchange} du to older than ${configs.maxHoursToHoldOrder} hours`)
await this.bittrex.cancel({uuid: openOrder.OrderUuid})
 }
 }
 log.info(`Canceled old orders ${(now() - start).toFixed(5)} ms`)
 }
} 
```

我添加了一个配置变量来控制订单等待时间。

```
const configs = {
 cancelOpenOrdersOnStart: true ,
 maxHoursToHoldBalance: 168, // 7 days
 refreshTimeout: 180000, // 3 minutes
} 
```

#### 第 6.2 步:填写货币

我们获取当前市场，只过滤 BTC 市场。我暂时不支持 ETH 市场。

```
async fetchMarkets () {
const start = now()
this.currencies = await this.bittrex.getMarkets()

 // remove other market than BTC for now
this.currencies = this.currencies.filter(c =\> c.BaseCurrency === 'BTC')
 log.info(`Fetched currencies ${(now() - start).toFixed(5)} ms`)
} 
```

然后，我们需要注入硬币的当前余额，以从我们的钱包中看到它们的实际价值。我也是现阶段把比特币分开。

```
async fetchBalances () {
const start = now()
const balances = await this.bittrex.getBalances()
for ( let currency of this.currencies) {
const balance = balances.find((b) =\> b.Currency === currency.MarketCurrency)
if (balance) {
 currency.balance = balance
 } else {
 currency.balance = null  
}
 }

this.bitcoin = balances.find((b) =\> b.Currency === 'BTC')

 log.info(`Fetched balances ${(now() - start).toFixed(5)} ms`)
} 
```

#### 步骤 6.3:获取市场实时数据

为了获得出价、要价和最新价格，我们需要将市场摘要注入到我们的货币中。

```
async injectMarketSummaries (data) {
const start = now()

const marketSummaries = await this.bittrex.getMarketSummaries({\_: Date.now()})

await Promise.map(data, async (d) =\> {
const marketSummary = marketSummaries.find((ms) =\> d.BaseCurrency === ms.Market.BaseCurrency && d.MarketCurrency === ms.Market.MarketCurrency)
 d.marketSummary = marketSummary.Summary
 })

 log.info(`Injected market summaries ${(now() - start).toFixed(5)} ms`)
} 
```

#### 第 6.4 步:过滤您的货币

我添加了我想交易的特定市场。

```
const configs = {
 cancelOpenOrdersOnStart: true ,
 maxHoursToHoldOrder: 0.5,
 refreshTimeout: 180000, // 3 minutes
 markets: [
 'ADA',
 'BCC',
 'DASH'
 ]
} 
```

您也可以按音量或特定的 BTC 值进行过滤。或者你只是不过滤和交易所有可用的硬币。

```
async filterCurrenciesByMarkets (markets) {
const start = now()

this.currencies = this.currencies.filter(c =\> markets.includes(c.MarketCurrency) || (c.balance && c.balance.Available \> 0))

 log.info(`Filtered currencies by selected markets ${(now() - start).toFixed(5)} ms`)
} 
```

#### 步骤 6.5:点燃蜡烛

为了分析一些市场指标，我们需要蜡烛。蜡烛线描述了所选市场在给定时间间隔内的价值。

<figure>[![](img/4249415b76cddfeab5e64cfd4a6b9f83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D7_9vPd9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/525/0%2AuSWzgqSY4E7MsRJz.gif)

<figcaption>[http://globaltrendtraders . com/technical-analysis/technical-analysis-of-stock-trends-1-chart-types/](http://globaltrendtraders.com/technical-analysis/technical-analysis-of-stock-trends-1-chart-types/)</figcaption>

</figure>

你可以在这里阅读更多关于蜡烛的信息:

[股票趋势技术分析#1 -图表类型](http://globaltrendtraders.com/technical-analysis/technical-analysis-of-stock-trends-1-chart-types/)

```
async injectCandleData (data) {
const start = now()
const \_ = Date.now()

const USDT\_BTC = await this.bittrex.getCandles({
 marketName: 'USDT-BTC',
 tickInterval: configs.tickInterval,
 \_
 })

await Promise.map(data, async (d) =\> {
 d.candles = await this.bittrex.getCandles({
 marketName: `BTC-${d.MarketCurrency}`,
 tickInterval: configs.tickInterval,
 \_
 })

 d.candles = d.candles.map((c, i) =\> this.parseCandleData(c, USDT\_BTC[i]))
 }, {concurrency: 15})

 log.info(`Injected candle data ${(now() - start).toFixed(5)} ms`)
}

parseCandleData (d, USDT\_BTC) {
return {
 date: parseDate(d.T),
 open: USDT\_BTC.O \* d.O,
 high: USDT\_BTC.H \* d.H,
 low: USDT\_BTC.L \* d.L,
 close: USDT\_BTC.C \* d.C,
 volume: d.V
 }
} 
```

步长间隔是一个名为 tickInterval 的新配置变量。你可以去低至五分钟或长达几个小时。我用一个小时，因为它更强大，我不想快速交易。

```
const configs = {
 cancelOpenOrdersOnStart: true ,
 maxHoursToHoldOrder: 0.5,
 refreshTimeout: 180000, // 3 minutes
 tickInterval: 'hour',
 markets: [
 'ADA',
 'BCC',
 'DASH'
 ]
} 
```

也不是说我用 BTC USDT 的值相乘。Bittrex 总是显示一个 BTC 值。因为我不想在给定的 BTC 上交易，而是在货币的实际美元价值上交易，所以我必须计算一下。

如果你想在原始的 BTC 价值曲线上交易，你可以跳过 USDT BTC 的部分。

#### 第 6.6 步:咀嚼数字

是时候拿我们的市场指标了。但是等等！不用自己算。有一个包可以解决这个问题。将 react stockcharts 与 react 一起安装，你就可以免费得到数学。

[rrag/react-stockcharts](https://github.com/rrag/react-stockcharts)

我想用 EMA。我们还需要一个时间和日期解析器。

```
import { timeParse } from'd3-time-format'
import { ema } from'react-stockcharts/lib/indicator'

const parseDate = timeParse('%Y-%m-%dT%H:%M:%S') 
```

首先让我们注入我们的市场指标。

```
async injectMarketIndicators (data) {
const start = now()

await Promise.map(data, async (d) =\> {
 d.keyFigures = await this.calculateMarketIndicators(d.candles)
 })

 log.info(`Calculated key figures ${(now() - start).toFixed(5)} ms`)
}

calculateMarketIndicators (data) {
const ema50 = ema()
 .id(0)
 .options({windowSize: 50})
 .merge((d, c) =\> {d.ema50 = c})
 .accessor(d =\> d.ema50)

const ema15 = ema()
 .id(1)
 .options({windowSize: 15})
 .merge((d, c) =\> {d.ema15 = c})
 .accessor(d =\> d.ema15)

return ema50(ema15(data))
} 
```

窗口大小定义了计算指标的时间跨度。

#### 步骤 6.7:计算自上次均线交叉以来的步数

当均线交叉时，我定义了一个时间点，我定义的两个均线值(EMA50 和 EMA15)交叉了。比如加密货币的盈亏平衡。

```
async countEmaCrossPointTicks (data) {
const start = now()

await Promise.map(data, async (d) =\> {
const lastKeyFigures = d.keyFigures[d.keyFigures.length - 1]
const lastEmaDifference = lastKeyFigures.ema50 - lastKeyFigures.ema15

 d.positiveTicks = 0
 d.negativeTicks = 0
for ( let i = d.keyFigures.length - 1; i \> 0; i--) {
const keyFigures = d.keyFigures[i]

if (lastEmaDifference \> 0 && keyFigures.ema50 - keyFigures.ema15 \> 0) {
 d.negativeTicks++
 } else if (lastEmaDifference \< 0 && keyFigures.ema50 - keyFigures.ema15 \< 0) {
 d.positiveTicks++
 } else {
break  
}
 }
 })

 log.info(`Counted ticks since last ema crossing ${(now() - start).toFixed(5)} ms`)
} 
```

现在每种货币都有正或负的步数计数器。这告诉我在这个特定的时间点投资这种货币是好是坏。

#### 步骤 6.8:注入成交订单历史

过去，我会根据我买了那枚硬币后赚了多少钱来做决定。或者自从上一次下单以来我损失了多少。目前我不使用这些信息。

```
async injectClosedOrderHistory (data) {
const start = now()

await Promise.map(data, async (d) =\> {
 d.orderHistory = await this.bittrex.getOrderHistory({
 market: `BTC-${d.MarketCurrency}`
 })
 }, {concurrency: 10})

 log.info(`Injected closed orders ${(now() - start).toFixed(5)} ms`)
} 
```

> 每种货币现在都有一个 orderHistory 数组，其中充满了旧订单。遗憾的是，Bittrex 只提供过去 30 天或总共 500 个条目的订单。所以要小心使用。

#### 步骤 6.9:刷新市场历史

当我们计算和处理这些数字的时候，时间已经过去了，最新的货币价值可能会在这个时候出错。所以我们在循环中刷新它。

```
await this.injectMarketSummaries( this.currencies) 
```

#### 第 6.10 步:决定在哪里投资，卖什么

[![](img/a413b48cff208e8aa3e12dceed9c2e62.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZhFuJibj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AO1Xk_bMt8kkB1xhi.jpg)

这将是最困难的部分。什么时候是投资一种货币的最佳时机？什么时候拒绝当前的投资并重新开始更好？什么时候你什么都不做，只是等待？

我决定只看均线交叉和均线的长度。这就是我数滴答的原因。我还添加了一个最小分笔成交点计数器值配置变量来防止投资跳跃(短时间内买入、卖出、买入、卖出)。

```
const configs = {
 cancelOpenOrdersOnStart: true ,
 maxEffectiveLoss: 60, // 60%
 maxHoursToHoldOrder: 0.5,
 refreshTimeout: 180000, // 3 minutes
 tickInterval: 'hour',
 markets: [
 'ADA',
 'BCC',
 'DASH'
 ],
 minimumEmaTicks: 5
} 
```

我还引入了一个最大损失上限来拒绝那些跌入低谷的投资。

```
async evaluateInvestHoldOrReject (data) {
const start = now()

await Promise.map(data, async (d) =\> {
const lastKeyFigures = d.keyFigures[d.keyFigures.length - 1]

 d.holdOrReject = INVEST\_HOLD\_OR\_REJECT.HOLD

if (d.balance && d.balance.Available \> 0) {
if (lastKeyFigures.ema15 \< lastKeyFigures.ema50 && d.negativeTicks \>= configs.minimumEmaTicks) {
 log.info(`Will reject ${d.MarketCurrency} due to ${d.negativeTicks} negative ema ticks`)
 d.holdOrReject = INVEST\_HOLD\_OR\_REJECT.REJECT
 } else if (d.balance.Available \* d.marketSummary.Bid \< configs.btcPerInvest \* (1 - configs.maxEffectiveLoss / 100)) {
 log.info(`Will reject ${d.MarketCurrency} due to falling below ${configs.btcPerInvest} btc`)
 d.holdOrReject = INVEST\_HOLD\_OR\_REJECT.REJECT
 }
 } else if (!d.balance || (d.balance && d.balance.Available === 0)) {
if (lastKeyFigures.ema15 \> lastKeyFigures.ema50 && d.positiveTicks \>= configs.minimumEmaTicks) {
 log.info(`Will invest in ${d.MarketCurrency} due to ${d.positiveTicks} positive ema ticks. EMA15 ${lastKeyFigures.ema15} \> EMA50 ${lastKeyFigures.ema50}`)
 d.holdOrReject = INVEST\_HOLD\_OR\_REJECT.INVEST
 }
 }
 })
 log.info(`Evaluated invest hold or reject ${(now() - start).toFixed(5)} ms`)
} 
```

#### 步骤 6.11:拒绝不良投资

当你认为投资不好时，它们就会被卖掉。所以让我们这样做吧。

```
async rejectBadInvestments (data) {
const start = now()

await Promise.map(data, async (d) =\> {
if (d.holdOrReject === INVEST\_HOLD\_OR\_REJECT.REJECT) {
if (d.marketSummary.Bid \* d.balance.Available \>= configs.minimumSellBalanceInBTC && d.balance.Available \> d.MinTradeSize) {
try {
await this.bittrex.sellLimit({
 market: `${d.BaseCurrency}-${d.MarketCurrency}`,
 quantity: d.balance.Available,
 rate: (d.marketSummary.Bid - configs.rateSellBuyExtraBtc)
 })
 } catch (e) {
 log.info(e)
 }
 log.info(`${d.MarketCurrency} placed REJECT SELL order`)
 }
 }
 }, {concurrency: 20})
 log.info(`Rejected investments ${(now() - start).toFixed(5)} ms`)
} 
```

我添加了 configs.rateSellBuyExtraBtc 来添加少量的 Btc 以确保买卖订单。此外，还添加了 configs . minimumsellbalanceinbtc 来检查 bittrex 销售阈值。Bittrex 不允许订单少于这个数。

```
const configs = {
 cancelOpenOrdersOnStart: true ,
 minimumSellBalanceInBTC: 0.0005,
 maxEffectiveLoss: 60, // 60%
 maxHoursToHoldOrder: 0.5,
 refreshTimeout: 180000, // 3 minutes
 rateSellBuyExtraBtc: 0.0000000000001, // to secure buy or sell
 tickInterval: 'hour',
 markets: [
 'ADA',
 'BCC',
 'DASH'
 ],
 minimumEmaTicks: 3
} 
```

#### 第 6.12 步:投入全部比特币！

现在让我们把所有剩余的比特币价值投入到看起来物有所值的货币中。我按照均线分类，首先投资新的上涨者。

我还引入了两个新的配置变量。

```
const configs = {
 btcBuffer: 0.01, // btc that has to stay on the bank
 btcPerInvest: 0.005,
 cancelOpenOrdersOnStart: true ,
 minimumSellBalanceInBTC: 0.0005,
 maxEffectiveLoss: 60, // 60%
 maxHoursToHoldOrder: 0.5,
 refreshTimeout: 180000, // 3 minutes
 rateSellBuyExtraBtc: 0.0000000000001, // to secure buy or sell
 tickInterval: 'hour',
 markets: [
 'ADA',
 'BCC',
 'DASH'
 ],
 minimumEmaTicks: 3
} 
```

*   btcPerInvest 定义了一个固定的 BTC 值，该值将用于实现该订单。你也可以根据你可用的比特币指数化地衡量这个价值。
*   btcBuffer 定义了一个固定的 btc 值，该值必须保持不变，不会用于投资。我喜欢为我的手动交易留一些 btc。

```
async invest (data) {
const start = now()

let buyCounter = 0

 // Sort by tick count from last ema crossing
 data.sort((a, b) =\> a.positiveTicks - b.positiveTicks)
for ( let d of data) {
if ( this.bitcoin.Available \> configs.btcPerInvest + configs.btcBuffer) {
if (d.holdOrReject === INVEST\_HOLD\_OR\_REJECT.INVEST) {
const quantity = (configs.btcPerInvest - 0.00000623) / d.marketSummary.Ask

if (quantity \> d.MinTradeSize) {
try {
await this.bittrex.buyLimit({
 market: `${d.BaseCurrency}-${d.MarketCurrency}`,
 quantity,
 rate: (d.marketSummary.Ask + configs.rateSellBuyExtraBtc)
 })
 buyCounter++
this.bitcoin.Available -= configs.btcPerInvest
 log.info(`Invested ${configs.btcPerInvest} bitcoin in ${d.MarketCurrency} for ${quantity} amount`)
 } catch (e) {
 log.info(e)
 }
 }
 }
 } else {
 log.info(`Not enough btc left to invest. Keep saving more!`)
break  
}
 }

 log.info(`Invested ${configs.btcPerInvest * buyCounter} btc in ${buyCounter} options ${(now() - start).toFixed(5)} ms`)
} 
```

### 第七步:部署

机器人完成了。我将代码推送到我的服务器上，用
启动它

```
$ docker-compose up --build -d 
```

这将建立和启动我的机器人。可以通过 docker-compose 的日志来查看日志。

### 第八步:改进你的机器人

你的机器人会犯错误，你会根据某些市场指标做出错误的投资决策。从中学习并改进您的代码。老实说，我的机器人在稳定下来并再次盈利之前损失了将近一半的钱。不要放弃。

[![](img/a7128cf1024e88573c7a71f1caa79a8d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VA52awXV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AzjEeAZCYHlyebNJP.jpg)

* * *