# Cypher 教程:匹配关键字

> 原文：<https://dev.to/sandordargo/cypher-tutorial-the-match-keyword-6c>

*本文最初发表在[我的博客](http://sandordargo.com/blog/2017/10/25/cypher-match)上。*

在[讨论了使用 CREATE 可以做什么，并略微提到了 MATCH](https://dev.to/sandordargo/cypher-tutorial-the-create-keyword-5p1/) 之后，让我们来探索一下 MATCH 在 Cypher 中可以为您做什么

幸运的是，在基本层面上，`CREATE`和`MATCH`都是不言自明的。所以有了`MATCH`，你将能够在你的数据库中寻找某些元素。简而言之，你可以匹配模式。通常你会将`MATCH`和`WHERE`一起使用，但不一定像我将向你展示的那样。

我们来快速看看 SQL 中对应的关键字有哪些。和往常一样，没有精确的匹配，但是如果你想到 SQL，你可以想到`FROM`和`WHERE`。但是在 SQL 中你严重依赖`WHERE`，在 Cypher 中只有`MATCH`可以在很多情况下为你做这些工作。

当我们要探究`MATCH`的用法时，我们还得了解另外两个关键词:`WHERE`和`RETURN`。稍后我会为他们每个人写一篇文章，现在让我们快速浏览一下。

#### **`WHERE`T4】**

这个关键字不能独立存在。它与下列条款中的任何一个相匹配:`MATCH`、`OPTIONAL MATCH`、`START`或`WITH`。这也意味着它可以以不同的方式使用。现在，我将只关注它与`MATCH`的基本用法，然后在另一篇文章中，我们将涵盖其余部分。

`WHERE`是给你在`MATCH`中定义的模式添加约束。在这些术语中，它的工作方式非常类似于 SQL。您可以使用常用的布尔运算符(`AND`、`OR`、`XOR`和`NOT`)，您可以使用它进行字符串匹配、量化比较、存在性检查等。这里有一个简短的(并非详尽的)例子列表:

```
WHERE region.name = 'Pannon' AND (grape.name = 'Merlot' OR grape.name = 'Cabernet franc') 
```

Enter fullscreen mode Exit fullscreen mode

```
WHERE subregion.size > 500 
```

Enter fullscreen mode Exit fullscreen mode

```
WHERE grape.name STARTS WITH 'Cabernet' 
```

Enter fullscreen mode Exit fullscreen mode

```
WHERE grape.name IN ['Muscat ottonel', 'Cabernet sauvignon'] 
```

Enter fullscreen mode Exit fullscreen mode

这些只是最简单的表达方式，更高级的例子将会在关于`WHERE`条款的文章中给出。

#### **`RETURN`T4】**

这就像 SQL 中的`SELECT`。但是在 SQL 中你以`SELECT`开始，在 Cypher 中你以`RETURN`结束。您可以定义希望从查询中返回的内容。最简单的形式，你可以返回一切:`RETURN *`。大多数时候，你会返回节点和/或关系:

```
MATCH (wineRegion:WineRegion)
RETURN wineRegion 
```

Enter fullscreen mode Exit fullscreen mode

```
MATCH ()-[relationship:GROWS_AT]->()
RETURN relationship 
```

Enter fullscreen mode Exit fullscreen mode

```
MATCH (grape:Grape)-[relationship:GROWS_AT]->(subregion:SubRegion)
RETURN grape, relationship, subregion 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，如果你想返回多个元素，这真的很容易，你可以只列出逗号分隔的元素。

如果你不想返回整个节点，只是一个单独的属性，这也很简单，你可以在节点/关系名称后指定属性名称，用点号隔开:

```
MATCH (wineRegion:WineRegion)
RETURN wineRegion.name 
```

Enter fullscreen mode Exit fullscreen mode

现在够了。先说一下`MATCH`关键词。

#### 获取所有节点

让我们从简单的开始。让我们返回所有节点。

```
MATCH (n)
RETURN n 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/1b13f36599e58db062128dc6a06029a3.png)T2】img/match_n.png)

就这么简单。匹配每个节点就行了。您可能期望在您的控制台上看到一堆孤立的节点，但是在这种情况下，您就错了。所有的关系也被检索。对于后一种说法，我必须说得更准确些。当您检索节点时，将检索这些节点之间的所有直接关系。

你很快就会明白我的意思。但首先，看一下我们今天将大量使用的子图。你可以看到一个名为 *Eger* 的区域中包含的所有葡萄酒分区，以及生长在这些分区中的所有葡萄。

[![](img/1e4f20e950c7499c0986247284f4cfbd.png)T2】img/eger-sub-graph.png)

#### 按名称获取节点

让我们假设有不同标签的同名节点。然而，出于某种原因，用两个不同的标签标记同一个节点是没有意义的。在我们的葡萄酒产区示例图中有这样一个例子。

*[埃格尔](https://en.wikipedia.org/wiki/Eger)* 是匈牙利的一座城市，也是一个葡萄酒产区和葡萄酒亚区的名称。如果您想获得所有同名的节点，而不声明您感兴趣的标签，您必须使用如下所示的`WHERE`子句。换句话说，你不能这样写:`MATCH (n:{name: 'Eger'})`。如果在`MATCH`子句中指定了一个属性，那么也必须指定标签。

```
MATCH (n)
 WHERE n.name = "Eger"
 RETURN n 
```

Enter fullscreen mode Exit fullscreen mode

#### 按标签和名称获取节点

您可以通过执行以下查询获得相同的结果:

```
MATCH (region:WineRegion{name:'Eger'}), (subregion:WineSubRegion{name:'Eger'})
RETURN region, subregion 
```

Enter fullscreen mode Exit fullscreen mode

在这两种情况下，您可以看到，当您返回这些节点时，它们彼此之间存在直接关系，这些关系也显示在 Neo4j 控制台上返回的图形中。但是，如果您检查其他视图，可以看到它们没有被返回，只是作为一种礼貌的表示。

现在，如果您查询两个相互之间有间接关系的节点，如葡萄和区域(中间有一个子区域)，即使在图形视图中您也看不到它们之间的任何关系，您只会看到两个单独的节点。

```
MATCH (grape:Grape {name:"Cabernet franc"}), (region:WineRegion {name:"Eger"})
RETURN grape, region 
```

Enter fullscreen mode Exit fullscreen mode

#### 方向不明的关系

如果您想要检索一些节点之间的任何直接关系，您可以这样做。你可以看到没有指向任何方向的箭头。甚至没有定义关系类型。我们只是将其命名为`rel`并将其返回。

```
MATCH (region:WineRegion{name:'Eger'})-[rel]-(subregion:WineSubRegion)
RETURN region, subregion, rel 
```

Enter fullscreen mode Exit fullscreen mode

#### 与定义方向的关系

现在我们用指向葡萄酒区域的关系编写查询。执行查询时，可以看到没有检索到任何节点或关系。

```
MATCH (region:WineRegion{name:'Eger'})<-[rel]-(subregion:WineSubRegion)
RETURN region, subregion, rel 
```

Enter fullscreen mode Exit fullscreen mode

让我们把那段感情的方向转过去吧！现在你可以看到，对于无向关系，我们得到了和以前一样的结果。事实上，图中的关系总是有方向的，但是 Cypher 引擎会寻找两个方向。

```
MATCH (region:WineRegion{name:'Eger'})-[rel]->(subregion:WineSubRegion)
RETURN region, subregion, rel 
```

Enter fullscreen mode Exit fullscreen mode

如果您想更具体一些，甚至可以指定关系的类型:

```
MATCH (region:WineRegion{name:'Eger'})-[rel:CONTAINS]->(subregion:WineSubRegion)
RETURN region, subregion, rel 
```

Enter fullscreen mode Exit fullscreen mode

或者您甚至可以指定多个关系类型来匹配:

```
MATCH (region:WineRegion{name:'Eger'})-[rel:CONTAINS|GROWS_AT]->(subregion:WineSubRegion)
RETURN region, subregion, rel 
```

Enter fullscreen mode Exit fullscreen mode

#### 获取关系的类型

假设你不知道某些节点是如何连接的。正如我们在您的查询中看到的，您不必指明关系类型。你可以得到所有的关系，不管它们的类型和方向。您可以检索它们的类型，而不是关系本身。或者两者都有！使用`type()`功能即可。

```
MATCH (region:WineRegion{name:'Eger'})-[rel:CONTAINS]->(subregion:WineSubRegion)
RETURN region, subregion, rel, type(rel) 
```

Enter fullscreen mode Exit fullscreen mode

关系只能用`type()`。

#### 按 id 获取节点或关系

如果您正在从应用程序构建和执行查询，那么您很可能会用它们的技术 id 来标识您的节点和/或关系。没问题，您可以通过使用`id()`函数使用它们的 id 轻松地检索它们。您可以轻松匹配一个或一系列 id。你可以看到这非常直观。

```
MATCH (n)
WHERE id(n) IN [0, 3, 5]
RETURN n 
```

Enter fullscreen mode Exit fullscreen mode

```
MATCH ()-[r]->()
WHERE id(r)= 0
RETURN r 
```

Enter fullscreen mode Exit fullscreen mode

我们还远未探索完`MATCH`提供的所有可能性，但今天就到此为止。我保留了一些关于通向高级文章的路径的主题。敬请期待！