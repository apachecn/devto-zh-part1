# 在 MongoDB 中使用索引排序

> 原文：<https://dev.to/damcosset/sorting-with-indexes-in-mongodb>

在本文中，我们将探讨 MongoDB 何时使用索引对文档进行排序。对于索引前缀，有几种情况需要注意。

首先，我们将在 mongo shell 中为数据库创建一些模拟数据。每个文档将有一个 I 字段，一个用户名字段和一个 createdAt 字段。

```
> for( i = 0; i < 10000; i++){
... db.users.insert({ i: i, username: 'user' + i, createdAt: new Date()})
}

> db.users.find({}).count()
10000 
```

Enter fullscreen mode Exit fullscreen mode

## 复合指标

接下来，让我们在集合上创建一个复合索引。复合索引是两个或多个字段的索引。对于这一个，我们将在用户名和我上有一个升序索引。

`db.users.createIndex({username: 1, i: 1})`

我们都准备好了，现在让我们使用复合索引对所有文档进行排序。我们将使用 explain()方法来获取关于 MongoDB 如何执行查询的信息:

```
> db.users.find({}).sort({username: 1, i:1}).explain("executionStats")

...

"winningPlan" : {
                        "stage" : "FETCH",
                        "inputStage" : {
                                "stage" : "IXSCAN",
                                "keyPattern" : {
                                        "username" : 1,
                                        "i" : 1
                                },
                                "indexName" : "username_1_i_1",
... 
```

Enter fullscreen mode Exit fullscreen mode

explain()输出表明我们使用了索引扫描来执行排序。我们使用集合扫描来查找集合中的所有文档，并使用索引扫描按照用户名和 I 的升序对它们进行排序。

注意，复合索引中的字段顺序很重要。如果我在排序谓词中颠倒 I 和用户名的顺序，一切都会改变:

```
> db.users.find({}).sort({i:1, username:1}).explain("executionStats")

...

"winningPlan" : {
                        "stage" : "SORT",
                        "sortPattern" : {
                                "i" : 1
                        },
                        "inputStage" : {
                                "stage" : "SORT_KEY_GENERATOR",
                                "inputStage" : {
                                        "stage" : "COLLSCAN",
                                        "direction" : "forward"
                                }
                        }
                },
... 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们的 winningPlan 不再使用索引扫描，而是使用内存排序。内存排序效率较低，所以我们在设计索引时必须小心。

## 索引前缀

索引前缀是复合索引的子集。在 MongoDB 中，即使只使用了复合索引某些字段，也可以使用索引扫描。但是，有一些规则:

*   索引前缀必须是连续的
*   索引前缀必须从左边开始

举几个例子。假设我有一个复合指数，如下所示:

`{username: 1, firstName: 1, lastName: 1}`

以下是有效的索引前缀:

*   `{username: 1, firstName: 1}`
*   `{username: 1}`

这些是**而不是**有效的索引前缀:

*   `{firstName: 1, lastName: 1}`
*   `{username: 1, lastName: 1}`
*   `{lastName: 1}`
*   `{lastName: 1, firstName: 1, username: 1}`

考虑到这一点，以下查询仍然会阻止内存中排序:

`db.users.find({}).sort({username: 1})`

## 索引前缀在查询和排序中重叠

您的索引前缀也可以在查询和排序谓词中重叠。他们遵循和以前一样的规则。例如:

```
> db.users.find({username: {$gt: 'user305'}}).sort({i:1}).explain('executionStats')

...

 "winningPlan" : {
                        "stage" : "SORT",
                        "sortPattern" : {
                                "i" : 1
                        },
                        "inputStage" : {
                                "stage" : "SORT_KEY_GENERATOR",
                                "inputStage" : {
                                        "stage" : "FETCH",
                                        "inputStage" : {
                                                "stage" : "IXSCAN",
                                                "keyPattern" : {
                                                        "username" : 1,
                                                        "i" : 1
                                                },
                                                "indexName" : "username_1_i_1",
                                                "isMultiKey" : false,
                                                "multiKeyPaths" : {
                                                        "username" : [ ],
                                                        "i" : [ ]
                                                },
                                                "isUnique" : false,
                                                "isSparse" : false,
                                                "isPartial" : false,
                                                "indexVersion" : 2,
                                                "direction" : "forward",
                                                "indexBounds" : {
                                                        "username" : [
                                                                "(\"user305\", {})"
                                                        ],
                                                        "i" : [
                                                                "[MinKey, MaxKey]"
                                                        ]
                                                }
                                        }
                                }
                        }
                },
... 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们的获胜计划仍然使用索引扫描来查询**和**排序，即使它们与我们的查询和排序谓词重叠。可能的组合和之前的一模一样。

## 索引方向

MongoDB 可以向前或向后读取索引。到目前为止，每次我们进行排序时，我们向前读取索引，因为我们的排序谓词使用了与复合索引中相同的排序方向。为了让 MongoDB 向后读取索引，您必须在每个索引字段中反转方向。比如:

```
> db.users.find({}).sort({username: -1, i: -1}).explain("executionStats")

...

 "winningPlan" : {
                        "stage" : "FETCH",
                        "inputStage" : {
                                "stage" : "IXSCAN",
                                "keyPattern" : {
                                        "username" : 1,
                                        "i" : 1
                                },
                                "indexName" : "username_1_i_1",
                                "isMultiKey" : false,
                                "multiKeyPaths" : {
                                        "username" : [ ],
                                        "i" : [ ]
                                },
                                "isUnique" : false,
                                "isSparse" : false,
                                "isPartial" : false,
                                "indexVersion" : 2,
                                "direction" : "backward",
                                "indexBounds" : {
                                        "username" : [
                                                "[MaxKey, MinKey]"
                                        ],
                                        "i" : [
                                                "[MaxKey, MinKey]"
                                        ]
                                }
                        }
                },
... 
```

Enter fullscreen mode Exit fullscreen mode

我们仍然像以前一样使用索引扫描，但是您可以看到这次“方向”键表示“向后”。

请再次注意，每个键都必须反转。再比如:

```
> db.users.find({}).sort({username: -1, i: 1}).explain("executionStats")

...

 "winningPlan" : {
                        "stage" : "SORT",
                        "sortPattern" : {
                                "username" : -1,
                                "i" : 1
                        },
                        "inputStage" : {
                                "stage" : "SORT_KEY_GENERATOR",
                                "inputStage" : {
                                        "stage" : "COLLSCAN",
                                        "direction" : "forward"
                                }
                        }
                },
... 
```

Enter fullscreen mode Exit fullscreen mode

这不使用索引扫描。在我的复合索引中，有一个字段是不反转的。我们退回到内存排序。

在反转排序方向时，您仍然可以使用索引前缀。如果我只使用`{username: -1}`，我仍然会得到一个索引扫描:

```
> db.users.find({}).sort({username: -1}).explain("executionStats")

...

                "winningPlan" : {
                        "stage" : "FETCH",
                        "inputStage" : {
                                "stage" : "IXSCAN",
                                "keyPattern" : {
                                        "username" : 1,
                                        "i" : 1
                                },
                                "indexName" : "username_1_i_1",
                                "isMultiKey" : false,
                                "multiKeyPaths" : {
                                        "username" : [ ],
                                        "i" : [ ]
                                },
                                "isUnique" : false,
                                "isSparse" : false,
                                "isPartial" : false,
                                "indexVersion" : 2,
                                "direction" : "backward",
                                "indexBounds" : {
                                        "username" : [
                                                "[MaxKey, MinKey]"
                                        ],
                                        "i" : [
                                                "[MaxKey, MinKey]"
                                        ]
                                }
                        }
                },

... 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我希望这能让您更好地了解 MongoDB 何时使用排序索引。目标是尽量避免低效内存排序。MongoDB 允许一种相当灵活的方法，但是它最终总是取决于索引的设计。