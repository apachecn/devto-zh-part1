# 理解解释()

> 原文：<https://dev.to/damcosset/understanding-explain>

## 简介

在 MongoDB 中，每当我们创建一个查询时，我们都必须问自己这个查询是否高效。重要的是优化这些操作，以确保我们正确使用我们拥有的资源。

说到查询， *explain()* 方法是一个很好的工具。 *explain()* 是了解查询过程中发生了什么的最好方法。

该方法将帮助您回答以下问题:

*   查询使用的是我们期望的索引吗？
*   我们的索引提供了排序吗？(或者查询使用的是内存排序？)
*   我们的查询使用索引来提供投影吗？
*   我们的指数有多大的选择性？
*   查询计划的哪个部分最昂贵？

## 创建数据和可解释的对象

首先，我们将导入一些数据。您可以在这个 [url](https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/primer-dataset.json) 检索 json 文件。

我把它保存为 *primer-dataset.json* 。接下来，我们需要导入我们的数据。为此，我们将使用 *mongoimport* 。确保您有一个 *mongod* 实例正在运行，并在新的终端窗口中键入以下命令(在命令中修改文件的路径):

`mongoimport --db test --collection restaurants --drop --file ~/Desktop/primer-dataset.json`

这将把我们的数据导入到*测试*数据库中的*餐馆*集合中。如果有名字为*餐馆*的收藏，它将首先删除。

让我们检查一下所有的东西。启动一个 *mongo* shell 并检查 test.restaurants:
的内容

```
> use test
switched to db test
> db.restaurants.find().count()
25359 
```

Enter fullscreen mode Exit fullscreen mode

完美！现在，让我们创建*可解释的对象*。 *explain()* 可以与三种不同的论点一起使用:

*   explain("queryPlanner") = >缺省值，如果您没有指定任何参数，就会有这个缺省值。
*   explain("executionStats") = >这个会给你关于获胜计划的详细信息。
*   explain("allPlansExecution") = >最详细。这将为您提供有关获胜计划和被拒绝计划的详细信息。

*注意:*默认 *explain()* 不**不**执行查询。“executionStats”和“allPlansExecution”将执行查询。

为了避免重复，让我们像这样创建可解释的对象:

```
> exp = db.restaurants.explain()
Explainable(test.restaurants)
> expExecStats = db.restaurants.explain("executionStats")
Explainable(test.restaurants)
> expVerbose = db.restaurants.explain("allPlansExecution")
Explainable(test.restaurants) 
```

Enter fullscreen mode Exit fullscreen mode

这将允许我们像这样运行查询:

`exp.find({restaurant_id : "50013447"})`

很好，现在让我们看看*解释*是怎么回事。

## 解释输出

让我们从默认输出(或“queryPlanner”)开始，查询如上所示:

```
> exp.find({restaurant_id : "50013447"})
{
        "queryPlanner" : {
                "plannerVersion" : 1,
                "namespace" : "test.restaurants",
                "indexFilterSet" : false,
                "parsedQuery" : {
                        "restaurant_id" : {
                                "$eq" : "50013447"
                        }
                },
                "winningPlan" : {
                        "stage" : "COLLSCAN",
                        "filter" : {
                                "restaurant_id" : {
                                        "$eq" : "50013447"
                                }
                        },
                        "direction" : "forward"
                },
                "rejectedPlans" : [ ]
        },
        "serverInfo" : {
                "host" : "pc4.home",
                "port" : 27017,
                "version" : "3.4.6",
                "gitVersion" : "c55eb86ef46ee7aede3b1e2a5d184a7df4bfb5b5"
        },
        "ok" : 1
} 
```

Enter fullscreen mode Exit fullscreen mode

每当我们试图优化一个查询时，最有趣的事情就是查看获胜的计划。这是 MongoDB 选择执行该查询的计划。在这种情况下，我们在集合上没有索引，因此，只有一个可用的计划。

如果我们有几个可用的计划，MongoDB 会在短时间内尝试每个计划，看看哪一个给出了最好的结果。*最好的*可能是完成所花费的时间或者在这段时间内返回的文档数量。

MongoDB 将缓存这个计划，因此它不会每次都为类似的查询比较可用的计划。但是，在某些事件之后，比如服务器重启或索引创建，MongoDB 将再次比较这些计划，看看是否可以使用更好的计划。

这里，我们做一个集合扫描(COLLSCAN)。没有任何索引，这没什么大不了的。让我们用 *executionStats* 运行同样的东西，这可能是你运行最多的一个:

```
> expExecStats.find({restaurant_id: "50013447"})
{
        "queryPlanner" : {
                "plannerVersion" : 1,
                "namespace" : "test.restaurants",
                "indexFilterSet" : false,
                "parsedQuery" : {
                        "restaurant_id" : {
                                "$eq" : "50013447"
                        }
                },
                "winningPlan" : {
                        "stage" : "COLLSCAN",
                        "filter" : {
                                "restaurant_id" : {
                                        "$eq" : "50013447"
                                }
                        },
                        "direction" : "forward"
                },
                "rejectedPlans" : [ ]
        },
        "executionStats" : {
                "executionSuccess" : true,
                "nReturned" : 1,
                "executionTimeMillis" : 52,
                "totalKeysExamined" : 0,
                "totalDocsExamined" : 25359,
                "executionStages" : {
                        "stage" : "COLLSCAN",
                        "filter" : {
                                "restaurant_id" : {
                                        "$eq" : "50013447"
                                }
                        },
                        "nReturned" : 1,
                        "executionTimeMillisEstimate" : 57,
                        "works" : 25361,
                        "advanced" : 1,
                        "needTime" : 25359,
                        "needYield" : 0,
                        "saveState" : 200,
                        "restoreState" : 200,
                        "isEOF" : 1,
                        "invalidates" : 0,
                        "direction" : "forward",
                        "docsExamined" : 25359
                }
        },
        "ok" : 1
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的输出中有一个新的键“executionStats”。让我们来分解一下:

*   nReturned:返回的文档数(1)
*   executionTimeMillis:执行查询的时间(52 毫秒)
*   totalKeysExamined:如果使用了索引，MongoDB 需要查看多少个键才能返回所需的文档(这里没有索引，所以为 0)
*   totalDocsExamined:MongoDB 必须检查多少文档才能返回我们需要的文档(25359)

我们马上就能看出这个查询一点也不高效。我们必须检查 25359 个文档来返回 1。这是有意义的，因为我们正在进行集合扫描。集合扫描意味着我们查看每一个文档，看它是否符合我们的过滤器。totalDocsExamined/nReturned 的比率需要更好。这就是索引有用的地方。

让我们在*餐馆 id* 字段上创建一个索引。

```
db.restaurants.createIndex({restaurant_id: 1})
{
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 2,
        "ok" : 1
} 
```

Enter fullscreen mode Exit fullscreen mode

很好，现在让我们再次运行同样的查询:

```
> expExecStats.find({restaurant_id: "50013447"})
{
        "queryPlanner" : {
                "plannerVersion" : 1,
                "namespace" : "test.restaurants",
                "indexFilterSet" : false,
                "parsedQuery" : {
                        "restaurant_id" : {
                                "$eq" : "50013447"
                        }
                },
                "winningPlan" : {
                        "stage" : "FETCH",
                        "inputStage" : {
                                "stage" : "IXSCAN",
                                "keyPattern" : {
                                        "restaurant_id" : 1
                                },
                                "indexName" : "restaurant_id_1",
                                "isMultiKey" : false,
                                "multiKeyPaths" : {
                                        "restaurant_id" : [ ]
                                },
                                "isUnique" : false,
                                "isSparse" : false,
                                "isPartial" : false,
                                "indexVersion" : 2,
                                "direction" : "forward",
                                "indexBounds" : {
                                        "restaurant_id" : [
                                                "[\"50013447\", \"50013447\"]"
                                        ]
                                }
                        }
                },
                "rejectedPlans" : [ ]
        },
        "executionStats" : {
                "executionSuccess" : true,
                "nReturned" : 1,
                "executionTimeMillis" : 9,
                "totalKeysExamined" : 1,
                "totalDocsExamined" : 1,
                "executionStages" : {
                        "stage" : "FETCH",
                        "nReturned" : 1,
                        "executionTimeMillisEstimate" : 0,
                        "works" : 2,
                        "advanced" : 1,
                        "needTime" : 0,
                        "needYield" : 0,
                        "saveState" : 0,
                        "restoreState" : 0,
                        "isEOF" : 1,
                        "invalidates" : 0,
                        "docsExamined" : 1,
                        "alreadyHasObj" : 0,
                        "inputStage" : {
                                "stage" : "IXSCAN",
                                "nReturned" : 1,
                                "executionTimeMillisEstimate" : 0,
                                "works" : 2,
                                "advanced" : 1,
                                "needTime" : 0,
                                "needYield" : 0,
                                "saveState" : 0,
                                "restoreState" : 0,
                                "isEOF" : 1,
                                "invalidates" : 0,
                                "keyPattern" : {
                                        "restaurant_id" : 1
                                },
                                "indexName" : "restaurant_id_1",
                                "isMultiKey" : false,
                                "multiKeyPaths" : {
                                        "restaurant_id" : [ ]
                                },
                                "isUnique" : false,
                                "isSparse" : false,
                                "isPartial" : false,
                                "indexVersion" : 2,
                                "direction" : "forward",
                                "indexBounds" : {
                                        "restaurant_id" : [
                                                "[\"50013447\", \"50013447\"]"
                                        ]
                                },
                                "keysExamined" : 1,
                                "seeks" : 1,
                                "dupsTested" : 0,
                                "dupsDropped" : 0,
                                "seenInvalidated" : 0
                        }
                }
        },
        "ok": 1
} 
```

Enter fullscreen mode Exit fullscreen mode

这一次，在我们的“executionStats”中，我们可以看到我们使用了索引扫描(IXSCAN)。“winningPlan”通知我们使用刚刚创建的索引，下面是这个查询的新指标:

*   返回:1
*   执行备忘录:9
*   totalkeyschecked:1
*   totalDocsExamined : 1

现在的比例是一比一，执行时间大大缩短了。

最后，我们来看看“allPlansExecution”的输出。我将创建两个索引:

```
> db.restaurants.createIndex({name: 1, cuisine: 1, borough: -1})
{
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 3,
        "ok" : 1
}
> db.restaurants.createIndex({name: 1})
{
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4,
        "ok" : 1
} 
```

Enter fullscreen mode Exit fullscreen mode

我在升序的 name 和 cuisine 字段和降序的 borough 字段上创建了一个复合索引。我在 name 字段上创建了另一个索引。让我们运行一个查询:

```
> expVerbose.find({name: "Subway"}).sort({cuisine: 1}) 
```

Enter fullscreen mode Exit fullscreen mode

这是一个很长的输出，所以我将首先显示获胜的计划:

```
 "winningPlan" : {
        "stage" : "FETCH",
        "inputStage" : {
                "stage" : "IXSCAN",
                "keyPattern" : {
                        "name" : 1,
                        "cuisine" : 1,
                        "borough" : -1
                },
                "indexName" : "name_1_cuisine_1_borough_-1",
                "isMultiKey" : false,
                "multiKeyPaths" : {
                        "name" : [ ],
                        "cuisine" : [ ],
                        "borough" : [ ]
                },
                "isUnique" : false,
                "isSparse" : false,
                "isPartial" : false,
                "indexVersion" : 2,
                "direction" : "forward",
                "indexBounds" : {
                        "name" : [
                                "[\"Subway\", \"Subway\"]"
                        ],
                        "cuisine" : [
                                "[MinKey, MaxKey]"
                        ],
                        "borough" : [
                                "[MaxKey, MinKey]"
                        ]
                }
        }
}, 
```

Enter fullscreen mode Exit fullscreen mode

对于这个查询，MongoDB 使用了我刚刚定义的复合索引。我们可以用`"indexName": "name_1_cuisine_1_borough_-1_"`来看这个

我们有了一个新的键“allPlansExecution”现在是我们的输出。让我们来看看“被拒绝的计划”:

```
 "rejectedPlans" : [
            {
                    "stage" : "SORT",
                    "sortPattern" : {
                            "cuisine" : 1
                    },
                    "inputStage" : {
                            "stage" : "SORT_KEY_GENERATOR",
                            "inputStage" : {
                                    "stage" : "FETCH",
                                    "inputStage" : {
                                            "stage" : "IXSCAN",
                                            "keyPattern" : {
                                                    "name" : 1
                                            },
                                            "indexName" : "name_1",
                                            "isMultiKey" : false,
                                            "multiKeyPaths" : {
                                                    "name" : [ ]
                                            },
                                            "isUnique" : false,
                                            "isSparse" : false,
                                            "isPartial" : false,
                                            "indexVersion" : 2,
                                            "direction" : "forward",
                                            "indexBounds" : {
                                                    "name" : [
                                                            "[\"Subway\", \"Subway\"]"
                                                    ]
                                            }
                                    }
                            }
                    }
            }
    ]
},
"allPlansExecution" : [
        {
                "nReturned" : 0,
                "executionTimeMillisEstimate" : 21,
                "totalKeysExamined" : 100,
                "totalDocsExamined" : 100,
                "executionStages" : {
                        "stage" : "SORT",
                        "nReturned" : 0,
                        "executionTimeMillisEstimate" : 21,
                        "works" : 101,
                        "advanced" : 0,
                        "needTime" : 101,
                        "needYield" : 0,
                        "saveState" : 2,
                        "restoreState" : 2,
                        "isEOF" : 0,
                        "invalidates" : 0,
                        "sortPattern" : {
                                "cuisine" : 1
                        },
                        "memUsage" : 45365,
                        "memLimit" : 33554432,
                        "inputStage" : {
                                "stage" : "SORT_KEY_GENERATOR",
                                "nReturned" : 100,
                                "executionTimeMillisEstimate" : 10,
                                "works" : 101,
                                "advanced" : 100,
                                "needTime" : 1,
                                "needYield" : 0,
                                "saveState" : 2,
                                "restoreState" : 2,
                                "isEOF" : 0,
                                "invalidates" : 0,
                                "inputStage" : {
                                        "stage" : "FETCH",
                                        "nReturned" : 100,
                                        "executionTimeMillisEstimate" : 0,
                                        "works" : 100,
                                        "advanced" : 100,
                                        "needTime" : 0,
                                        "needYield" : 0,
                                        "saveState" : 2,
                                        "restoreState" : 2,
                                        "isEOF" : 0,
                                        "invalidates" : 0,
                                        "docsExamined" : 100,
                                        "alreadyHasObj" : 0,
                                        "inputStage" : {
                                                "stage" : "IXSCAN",
                                                "nReturned" : 100,
                                                "executionTimeMillisEstimate" : 0,
                                                "works" : 100,
                                                "advanced" : 100,
                                                "needTime" : 0,
                                                "needYield" : 0,
                                                "saveState" : 2,
                                                "restoreState" : 2,
                                                "isEOF" : 0,
                                                "invalidates" : 0,
                                                "keyPattern" : {
                                                        "name" : 1
                                                },
                                                "indexName" : "name_1",
                                                "isMultiKey" : false,
                                                "multiKeyPaths" : {
                                                        "name" : [ ]
                                                },
                                                "isUnique" : false,
                                                "isSparse" : false,
                                                "isPartial" : false,
                                                "indexVersion" : 2,
                                                "direction" : "forward",
                                                "indexBounds" : {
                                                        "name" : [
                                                                "[\"Subway\", \"Subway\"]"
                                                        ]
                                                },
                                                "keysExamined" : 100,
                                                "seeks" : 1,
                                                "dupsTested" : 0,
                                                "dupsDropped" : 0,
                                                "seenInvalidated" : 0
                                        }
                                }
                        }
                }
        },
        {
                "nReturned" : 101,
                "executionTimeMillisEstimate" : 0,
                "totalKeysExamined" : 101,
                "totalDocsExamined" : 101,
                "executionStages" : {
                        "stage" : "FETCH",
                        "nReturned" : 101,
                        "executionTimeMillisEstimate" : 0,
                        "works" : 101,
                        "advanced" : 101,
                        "needTime" : 0,
                        "needYield" : 0,
                        "saveState" : 2,
                        "restoreState" : 2,
                        "isEOF" : 0,
                        "invalidates" : 0,
                        "docsExamined" : 101,
                        "alreadyHasObj" : 0,
                        "inputStage" : {
                                "stage" : "IXSCAN",
                                "nReturned" : 101,
                                "executionTimeMillisEstimate" : 0,
                                "works" : 101,
                                "advanced" : 101,
                                "needTime" : 0,
                                "needYield" : 0,
                                "saveState" : 2,
                                "restoreState" : 2,
                                "isEOF" : 0,
                                "invalidates" : 0,
                                "keyPattern" : {
                                        "name" : 1,
                                        "cuisine" : 1,
                                        "borough" : -1
                                },
                                "indexName" : "name_1_cuisine_1_borough_-1",
                                "isMultiKey" : false,
                                "multiKeyPaths" : {
                                        "name" : [ ],
                                        "cuisine" : [ ],
                                        "borough" : [ ]
                                },
                                "isUnique" : false,
                                "isSparse" : false,
                                "isPartial" : false,
                                "indexVersion" : 2,
                                "direction" : "forward",
                                "indexBounds" : {
                                        "name" : [
                                                "[\"Subway\", \"Subway\"]"
                                        ],
                                        "cuisine" : [
                                                "[MinKey, MaxKey]"
                                        ],
                                        "borough" : [
                                                "[MaxKey, MinKey]"
                                        ]
                                },
                                "keysExamined" : 101,
                                "seeks" : 1,
                                "dupsTested" : 0,
                                "dupsDropped" : 0,
                                "seenInvalidated" : 0
                        }
                }
        }
]
}, 
```

Enter fullscreen mode Exit fullscreen mode

我删除了我们获胜计划的“执行状态”。我们将只关注“allPlansExecution”。

首先，我们有关于被 MongoDB 拒绝的计划的信息。我们可以看到这是使用索引`{name: 1}`的计划。为什么会拒绝呢？嗯，我们可以看到它首先进行了索引扫描，但是当它进行排序时，我们读取的是`"stage" : "SORT_KEY_GENERATOR"`。这意味着我们做了内存排序。内存排序实际上并不高效。

但是我们来看看“allPlansExecution”中的指标。这里我们可以看到为什么选择一个计划。让我们比较一下这两个计划:

*   返回:0 比 101
*   执行时间:21 比 0
*   totalKeysExamined: 100 比 101
*   totalDocsExamined: 100 比 101

MongoDB 运行了两个候选计划。在短时间内，两个计划都必须证明它们的价值，这是我们的衡量标准。一个计划在 21 毫秒内没有返回任何文档，另一个计划立即返回了 101 个文档。有了这些指标，就很容易理解为什么 MongoDB 选择使用复合索引`{name: 1, cuisine: 1, borough: -1}`来运行这个查询了。`{name: 1}`指数根本无法竞争。

我希望这篇介绍能让你更好地了解 *explain()* 能为你做些什么。您将能够看到哪些查询是无效的，并相应地调整与数据库交互的方式。