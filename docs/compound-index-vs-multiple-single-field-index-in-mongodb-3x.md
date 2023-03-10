# MongoDB 3.x 中的复合索引与多个单字段索引

> 原文：<https://dev.to/weiiishannn/compound-index-vs-multiple-single-field-index-in-mongodb-3x>

# MongoDB 3.x 中的复合索引与多个单字段索引

MongoDB 同时支持[复合索引和](https://docs.mongodb.com/manual/indexes/)单字段索引。众所周知，索引会降低插入/更新的性能，但会提高选择的性能。但是，如果您需要索引多个字段，那么使用复合索引好还是只创建多个单字段索引好呢？这篇文章试图调查哪一个选项在性能方面更好。

## **插入表演**

### 测试设置:

*   在我的 Mac 上运行的单个 MongoDB 3.2 实例
*   [POC 驱动程序](https://github.com/johnlpage/POCDriver)使用单线程仅执行插入
*   试运行 5 分钟
*   每次运行后清空集合
*   默认读取关注
*   默认写关注

**样本文档:**

```
> db.testCol.findOne()
{
 "_id" : {
 "w" : 2,
 "i" : 0
 },
 "fld0" : NumberLong(530752),
 "fld1" : ISODate("2015-06-02T08:14:18.687Z"),
 "fld2" : "tempor invidunt ut labore et",
 "fld3" : "eirmod tempor invidunt ut",
 "fld4" : "Lorem ipsum dolor sit amet,",
 "fld5" : ISODate("2016-10-12T01:58:24.580Z"),
 "fld6" : NumberLong(1712708),
 "fld7" : "sit amet. Lorem ipsum dolor",
 "fld8" : "sit amet, consetetur",
 "fld9" : NumberLong(652363),
 "bin" : BinData(0,"")
}
```

**阶段 1(除 on _id 字段外无索引)**

```
After 295 seconds, 13340672 new records inserted - collection has 13340672 in total
46498 inserts per second since last report 99.97 % in under 50 milliseconds
0 keyqueries per second since last report 100.00 % in under 50 milliseconds
0 updates per second since last report 100.00 % in under 50 milliseconds
0 rangequeries per second since last report 100.00 % in under 50 milliseconds
```

**~每秒 45，222 次插入**

**第 2 期(复合指数 on-fld 1-fld 9)**

```
After 295 seconds, 7739904 new records inserted - collection has 7739904 in total
20830 inserts per second since last report 99.72 % in under 50 milliseconds
0 keyqueries per second since last report 100.00 % in under 50 milliseconds
0 updates per second since last report 100.00 % in under 50 milliseconds
0 rangequeries per second since last report 100.00 % in under 50 milliseconds
```

**~每秒 26，236 次插入**

**第三阶段(fld2、fld6 和 fld8 上的单字段索引)**

```
After 295 seconds, 2998272 new records inserted - collection has 2998272 in total
7012 inserts per second since last report 63.61 % in under 50 milliseconds
0 keyqueries per second since last report 100.00 % in under 50 milliseconds
0 updates per second since last report 100.00 % in under 50 milliseconds
0 rangequeries per second since last report 100.00 % in under 50 milliseconds
```

**~每秒 10，163 次插入**

## **【存储使用】**

***同一个集合上有 2817024 个文档:***

**复合指数**

```
db.testCol.stats().indexSizes
{
 "_id_" : 75079680,
 "fld0_1_fld1_1_fld2_1_fld3_1_fld4_1_fld5_1_fld6_1_fl7_1_fld8_1_fld9_1" : 2617061376
}
```

**~2.44GB**

**单字段指标**

```
> db.testCol.stats().indexSizes
{
 "_id_" : 75079680,
 "fld0_1" : 65343488,
 "fld1_1" : 79781888,
 "fld2_1" : 35471360,
 "fld3_1" : 35475456,
 "fld4_1" : 35475456,
 "fld5_1" : 79781888,
 "fld6_1" : 65339392,
 "fld7_1" : 35475456,
 "fld8_1" : 35475456,
 "fld9_1" : 65347584,
}
```

**~ 0.53 GB**T3】

## 指标选择性使用

对于单字段索引，没有任何限制。MongDB optimiser 将挑选它认为合适的索引。对于复合指数，略有不同。

像这样的查询不会使用复合索引:

*   db.testCol.find({fld9:1，fld8:1，fld7:1})
*   db.testCol.find({fld9:1，fld3:1，fld6:1})
*   db.testCol.find({fld9:1，fld8:1，fld7:1})

像这样的查询将使用复合索引:

*   db.testCol.find({fld0:1，fld1:1})
*   db.testCol.find({fld5:1，fld8:1，fld0:1})(效率不是很高..)
*   db.testCol.find({fld0:1，fld3:1，fld5:1})

一般的想法是，如果它与索引前缀匹配，MongoDB 将使用它作为复合索引。如果匹配不正确，它可能仍然使用索引，如果它仍然比执行完全集合扫描更有效的话。找到答案的唯一方法是[教育](https://www.mongodb.com/collateral/mongodb-performance-best-practices)并自己测试这些查询！

## 总结

下面是对中关键点的简短总结:

1.  没有哪个索引具有最好的插入性能(咄！！)
2.  在插入性能方面，复合索引比单字段索引快
3.  复合索引比单字段索引使用更多的存储空间

视情况而定，谨慎选择你的毒药:)

**参考文献** :

*   https://github.com/johnlpage/POCDriver
*   https://docs.mongodb.com/v3.2/core/index-compound/
*   https://docs.mongodb.com/v3.2/core/index-single/
*   https://docs.mongodb.com/manual/indexes/
*   https://www.mongodb.com/collateral/mongodb-performance-best-practices

页（page 的缩写）s:上面的测试并不试图量化绝对的性能数字，而是为索引提供一个良好的性能下降率

魏善传旨