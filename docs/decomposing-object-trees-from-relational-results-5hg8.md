# 从关系结果中分解对象树

> 原文：<https://dev.to/dmfay/decomposing-object-trees-from-relational-results-5hg8>

这是我最近添加到我的开源项目 Massive.js 中的一个[特性](https://dmfay.github.io/massive-js/options.html#decomposition-schemas)。我曾经在多个`JOIN` ed 表的层次结构上查询视图来引用数据。例如，下面的查询返回了一个葡萄酒厂列表、一些葡萄酒以及每个葡萄酒厂的葡萄:

```
SELECT ws.id, ws.name, ws.country, w.id AS wine_id, w.name AS wine_name, w.year,
  va.id AS varietal_id, va.name AS varietal_name
FROM wineries ws
JOIN wines w ON w.winery_id = ws.id
JOIN wine_varietals wv ON wv.wine_id = w.id
JOIN varietals va ON va.id = wv.varietal_id
ORDER BY w.year; 
```

Enter fullscreen mode Exit fullscreen mode

结果集如下所示:

```
 id |         name         | country | wine_id |       wine_name       | year | varietal_id |   varietal_name    
---------+----------------------+---------+---------+-----------------------+------+-------------+--------------------
  4 | Chateau Ducasse      | FR      |       7 | Graves                | 2010 |           6 | Cabernet Franc
  2 | Bodega Catena Zapata | AR      |       5 | Nicolás Catena Zapata | 2010 |           4 | Malbec
  2 | Bodega Catena Zapata | AR      |       5 | Nicolás Catena Zapata | 2010 |           1 | Cabernet Sauvignon
  4 | Chateau Ducasse      | FR      |       7 | Graves                | 2010 |           5 | Merlot
  4 | Chateau Ducasse      | FR      |       7 | Graves                | 2010 |           1 | Cabernet Sauvignon
  3 | Domäne Wachau        | AT      |       6 | Terrassen Federspiel  | 2011 |           7 | Grüner Veltliner
  1 | Cass Vineyards       | US      |       1 | Grenache              | 2013 |           2 | Grenache
  1 | Cass Vineyards       | US      |       2 | Mourvedre             | 2013 |           3 | Mourvedre
  2 | Bodega Catena Zapata | AR      |       3 | Catena Alta           | 2013 |           4 | Malbec
  2 | Bodega Catena Zapata | AR      |       4 | Catena Alta           | 2013 |           1 | Cabernet Sauvignon 
```

Enter fullscreen mode Exit fullscreen mode

这告诉我们很多:我们有两种来自卡斯的单品种葡萄酒，两种(注意不同的`wine_id` s)和一种来自凯特娜的混合葡萄酒，一种来自瓦豪的 grüner，还有一种来自迪卡斯的经典波尔多混合葡萄酒。但是，虽然我可以很容易地从这个结果集中挑选出我感兴趣的信息，但它不能被应用程序代码直接使用，应用程序代码一次处理一条记录。如果我需要使用这些结果来驱动一个提供酒厂简介并允许用户深入了解他们的产品的网站，我会很难受。那个结构看起来更像这样:

```
├── Bodega Catena Zapata
│   ├── Catena Alta
│   │   └── Cabernet Sauvignon
│   ├── Catena Alta
│   │   └── Malbec
│   └── Nicolás Catena Zapata
│   ├── Cabernet Sauvignon
│   └── Malbec
├── Cass Vineyards
│   ├── Grenache
│   │   └── Grenache
│   └── Mourvedre
│   └── Mourvedre
├── Chateau Ducasse
│   └── Graves
│   ├── Cabernet Franc
│   ├── Cabernet Sauvignon
│   └── Merlot
└── Domäne Wachau
    └── Terrassen Federspiel
        └── Grüner Veltliner 
```

Enter fullscreen mode Exit fullscreen mode

关系数据库根本做不好树。这是像 MongoDB 这样的文档数据库的一个吸引人的地方，它能够很容易地表示这种结构。然而，我们的数据实际上是相关的:我们也有“按葡萄搜索”功能，通过从`varietals`中的单个记录开始并执行外键扫描，可以更容易地挑选出与“Mourvedre”匹配的葡萄酒。甚至可以转位。相比之下，要使用文档数据库完成这项工作，您需要查看每一个文档，看它的`varietals`是否匹配，这仍然存在一个问题，即确保每个酒厂在输出中只出现一次。更糟糕的是，不能保证有人没有在某个地方打错“Moruvedre”。

有一个简单的方法来生成 profile-wine-variety 树:只需迭代结果集，看看我们是否有一个新的酒厂，如果有，就添加它，看看这个酒厂是否有新的葡萄酒，如果有，就添加它。这不是很有效，但无论如何，这不是一个人在数百万条记录规模上做的事情。更大的问题是它只对这些特定的结果有效。下次我遇到这种情况，我将不得不从头开始。我很懒。我只想把这个东西*写一遍*。

## 位置，位置，位置

第一个问题是确定哪些列属于对象树中的哪个位置。查询结果没有说明给定的列来自哪个表，即使说明了，也不能保证它真的属于那个表。意义是与上下文相关的:开发人员可能希望将 1:1 关系的合并结果合并到一个对象中，或者做一些我无法预料的更复杂的事情。

为了放置每个列，Massive 需要一个模式。定义任何类型的数据模型是我在项目中尽可能长时间避免的事情；我来自一个强类型的背景，这几乎是本能。除了许多优点之外，强类型是对象关系映射模式(O/RM)在 Java 和 C#等语言中主导数据访问的原因之一:提前映射类定义的要求非常容易将数据模型创建为对象图的并行表示。这就是“对象-关系阻抗不匹配”，也被称为计算机科学的[越南。您现在有两个数据模型，每个都与另一个不同步，每个都试图将数据硬塞进不太适合它的格式中。相比之下，JavaScript 基本上不关心对象是什么。这使得 Massive 不需要任何类型的建模:它从表、可查询内容和可执行内容中构建了一个 API，但之后它都是匿名结果对象的数组。](http://blogs.tedneward.com/post/the-vietnam-of-computer-science/)

在这段代码的早期版本中，我根据列别名自动生成了模式。字段`wines__id`将被分配给输出中名为`wines`的集合的一个元素。我最终放弃了这个:命名约定需要大量的前期工作，如果你试图对一个已经存在的视图这样做，它可能不遵循我刚刚提出的约定。这对 Massive 来说是毒药，Massive 应该是一个多功能的工具包，对你的模型没有什么期望。在调用时提供模式仍然是一项不可忽视的工作，但是您只有在绝对需要时才必须这样做。

一个模式看起来像这样:

```
{
  "pk": "id",
  "columns": ["id", "name", "country"],
  "wines": {
    "pk": "wine_id",
    "columns": {"wine_id": "id", "wine_name": "name", "year": "year"},
    "array": true,
    "varietals": {
      "pk": "varietal_id",
      "columns": {"varietal_id": "id", "varietal_name": "name"},
      "array": true
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

每个嵌套元素都定义了一个`pk`字段，我们将使用它来区分属于树中适当级别的不同对象的记录。`columns`可以是一个数组或对象，允许重命名(我们的每个表都有一个名为`name`的列，前缀只对平面结果集有意义)。内部模式上的`array`标志指示从模式创建的对象是否应该附加到集合中，或者作为父模式上的嵌套对象添加。我们没有后者的任何实例，但是对于具有丰富配置文件对象或另一种 1:1 关系的用户，您可以使用它。

## 把事情弄得一团糟

给定一个结果集和一个应用于它的模式，我们的首要任务是整合。迪卡斯酒庄在我们的数据集中只有一种葡萄酒，但是因为它是赤霞珠/梅洛/品丽珠混合的，所以它出现在三行中。由于排序引擎的某种原因，这三行甚至不相邻。如果我们只是积累数据直到`id`发生变化，我们会有麻烦——我们会有 2010 年迪卡斯酒庄的 cab franc 和 2010 年迪卡斯梅洛/cab sauv 的记录，这两个记录实际上都不存在。如果我们真的把 T2 做得很差，我们会有两个不同的迪卡斯庄园，每个庄园都有一瓶想象中的葡萄酒。

幸运的是，我们的模式定义了一个主键字段，这将确保迪卡斯城堡是唯一的迪卡斯城堡；我们有散列表。我们可以将查询结果表示为一个递归嵌套的字典，它将每个对象的主键与模式定义的字段的值相匹配。即使对于像我们这样相对较小的数据集，这种映射也会很快变大。这是迪卡斯城堡部分完整的样子:

```
{ ...,
  "4": {
    "id": 4,
    "name": "Chateau Ducasse",
    "country": "FR",
    "wines": {
      "7": {
        "id": 7,
        "name": "Graves",
        "year": 2010,
        "varietals": {
          "1": {
            "id": 1,
            "name": "Cabernet Sauvignon"
          },
          "5": {
            "id": 5,
            "name": "Merlot"
          },
          "6": {
            "id": 6,
            "name": "Cabernet Franc"
          }
        }
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了生成这个结果，我们对结果集进行迭代，并将每一行传递给一个函数，该函数递归地遍历模式树以应用记录数据。对于这个模式，我们从`wineries`开始，所以`id` 4 对应于迪卡斯城堡。在这个对象中，`wines`映射中的葡萄酒`id` 7 对应于他们的 2010 年波尔多葡萄酒，以此类推。

## 简化！

然而，主键映射很难使用。它实现了以树状结构而不是表格形式组织数据的目的；现在它需要消失，因为它是我们超级简单的酿酒厂-葡萄酒-品种树之上的额外一层复杂性。我们需要将外部字典中的每个酒厂值分解成它自己的对象，递归到每个对象中对它们的葡萄酒做同样的事情，最后递归到葡萄酒中处理品种。

如果这听起来真的和我们刚才做的很相似，那是因为它就是。从技术上来说，用一遍而不是两遍就可以做到这一点，但是将原始结果处理成哈希表要比我们可能要做的数组扫描次数快得多。

为了达到最终的格式，我们减少了映射的键列表；这些是示例数据集中每个酒厂的主键。来自映射的相应值进入`reduce`累加器。因为我们在这里只处理数组，所以累加器总是一个数组；如果我们有一个 1:1 关系的子对象，我们将通过关闭模式定义中的`array`来使用对象累加器。这将导致子对象可以作为其父对象的属性直接访问。

这是连环链:

```
[ ...,
  {
    "id": 2,
    "name": "Bodega Catena Zapata",
    "country": "AR",
    "wines": [ {
      "id": 3,
      "name": "Catena Alta",
      "year": 2013,
      "varietals": [ {
        "id": 4,
        "name": "Malbec"
      } ]
    }, {
      "id": 4,
      "name": "Catena Alta",
      "year": 2013,
      "varietals": [ {
        "id": 1,
        "name": "Cabernet Sauvignon"
      } ]
    }, {
      "id": 5,
      "name": "Nicolás Catena Zapata",
      "year": 2010,
      "varietals": [ {
        "id": 1,
        "name": "Cabernet Sauvignon"
      }, {
        "id": 4,
        "name": "Malbec"
      } ]
    } ]
  },
... ] 
```

Enter fullscreen mode Exit fullscreen mode

非常简单:我们有酿酒厂，酿酒厂有葡萄酒，葡萄酒有品种。所有内容都与原始查询结果中的真实主键值一致。我们已经将一个带有嵌入关系的原始结果集转化为这些关系的模型*。这在客户端代码的关系上下文之外更容易管理，并且它是我们希望用户拥有的心智模型的准确表示。该模式确实增加了一些开销，但是已经尽可能地控制住了。从现在开始，进一步的自动化只会降低灵活性。*