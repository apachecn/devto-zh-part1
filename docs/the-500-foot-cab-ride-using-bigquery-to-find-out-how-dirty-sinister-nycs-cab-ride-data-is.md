# 乘坐 500 英尺的出租车:使用 BigQuery 找出有多脏(险恶？)纽约市的出租车乘车数据是

> 原文：<https://dev.to/walker/the-500-foot-cab-ride-using-bigquery-to-find-out-how-dirty-sinister-nycs-cab-ride-data-is>

我可能晚了几年，但是谷歌的 BigQuery 太棒了。数据仓库是一堆真正整洁的数据集的家，用户可以以惊人的速度遍历这些数据集(而且是免费的，最多可达 1tb)。在过去的几周里，我研究了 GitHub 数据集，以[找到流行的 Python 包](https://dev.to/walker/using-googles-bigquery-to-better-understand-the-python-ecosystem)和咒骂程序员的[频率](https://dev.to/walker/-this-combing-through-183-million-commits-to-find-out-when-and-how-developers-us-the-f-bomb)。

今天，我将重现我去年写的一篇关于纽约出租车的博文。当我意识到出租车&豪华轿车委员会公布了他们的乘车数据，包括起点和终点的地理坐标时，我觉得有一些有趣的模式等待着被发现。

特别是，我想知道人们乘坐出租车进行可笑的短距离(不到十分之一英里)的频率，以及这些乘坐与更传统的乘坐有何不同。我最期待的是地理和时间上的差异:当然，只有喝醉的深夜顾客或富裕社区的居民才有理由坐出租车去几百英尺远的地方，对吗？

虽然没有太多的证据支持这一假设，但我确实发现了一些奇怪的东西。让我们将自 2009 年以来的所有黄色出租车分成长度超过 1/10 英里的(“正常”)和不超过 1/10 英里的(“微型”)出租车，以及拥有完整地理信息的出租车(上下车都在场)和没有地理信息的出租车。

在这四个组别中，我们将计算乘车用现金支付的频率，票价为 50 美元或以上的频率，以及票价谈判的频率。

(根据记录，该命令在不到 6 秒的时间内查询了超过 10 亿行。)

```
SELECT 
  CASE WHEN trip_distance > 0.1 THEN "normal" ELSE "micro-ride" END AS type,
  CASE WHEN pickup_longitude = 0 OR pickup_latitude = 0 
        OR dropoff_longitude = 0 OR dropoff_latitude = 0 
        THEN 'yes' ELSE 'no' END AS missing_geo,
  COUNT(*) AS count,
  STRING(ROUND(SUM(CASE WHEN payment_type = 'CSH' THEN 1 ELSE 0 END)/count*100, 0)) + '%' AS cash,
  STRING(ROUND(SUM(CASE WHEN total_amount  >= 50 THEN 1 ELSE 0 END)/count*100, 2)) + '%' AS fifty_plus,
  STRING(ROUND(SUM(CASE WHEN rate_code = '5' THEN 1 ELSE 0 END)/count*100,2)) + '%' AS negotiated
FROM [nyc-tlc:yellow.trips]
GROUP BY type, missing_geo
ORDER BY type DESC, missing_geo 
```

| 类型 | 缺少 _ 地理位置 | 数数 | 现金 | 五十多岁 | 谈判 |
| --- | --- | --- | --- | --- | --- |
| 常态 | 不 | One billion seventy-six million eight hundred and fifty-one thousand four hundred and thirty | 49.00% | 2.11% | 0.10% |
| 常态 | 是 | Twenty million four hundred and seventeen thousand seven hundred and thirty-three | 48.00% | 2.21% | 0.19% |
| 微型游乐设施 | 不 | Nine million eight hundred and twenty-eight thousand two hundred and seventy-two | 56.00% | 15.07% | 8.32% |
| 微型游乐设施 | 是 | One million six hundred and eighty-two thousand and twenty-eight | 55.00% | 25.53% | 26.63% |

其中一些趋势是可以预测的。每个微骑大概有 100 次正常骑行，其中大约一半时间使用现金。但是有些有点奇怪。为什么有这么多的微型游乐设施花费超过 50 美元，而它们按定义应该便宜得多？为什么这种趋势被夸大了，因为微型交通工具缺少地理位置数据？为什么缺少地理数据会使微型游乐设施的协商频率比普通游乐设施高得多？

这一切可能有合理的解释。但乍一看，我说服自己，这些数据背后有一些险恶的东西:在纽约市黑暗而复杂的世界里，有一类旅行，司机或乘客不希望他们的确切位置被记录下来，这些都是以协商好的价格用现金支付的相同的乘坐。随意用弯曲的出租车司机、砖块形状的包裹和上了膛的枪支的故事来填充细节。

或者，可能缺少坐标或距离不太可能的行已经被破坏，在这种情况下，我们的问题变成:数据是传统意义上的脏还是犯罪方式的脏？