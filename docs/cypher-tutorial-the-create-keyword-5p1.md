# Cypher 教程:CREATE 关键字

> 原文：<https://dev.to/sandordargo/cypher-tutorial-the-create-keyword-5p1>

*本文最初发表在[我的博客](http://sandordargo.com/blog/2017/10/11/cypher-create)上。*

在我的关于密码的系列文章的第一篇文章中，我向你介绍了 CREATE 关键字。

这个关键字的目的是将节点或关系添加到图形数据库中。正如您将要看到的，您可以添加一个没有任何有用数据的简单节点，但是您也可以添加多个具有良好特征的节点，并通过关系将它们连接在一起。我们深潜吧！

## 创建一个节点

我能想到的最简单的查询是这个:

```
CREATE () 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个没有任何标签或属性的新节点。它是一个纯节点，只有它的技术 ID。可能在现实生活中不太有用，但这仍然是 origo。这有助于我们了解在一个 Cypher 查询中，一个节点是在括号之间定义的。

### 创建一个带有一个或多个标签的节点

现在，如果你想创建一个带标签的新节点，你可以在冒号后面写标签，就像这样:

```
CREATE (:CABERNET_SAUVIGNON) 
```

Enter fullscreen mode Exit fullscreen mode

正如我在[简介](https://dev.to/blog/2017/10/04/cypher-introduction)中提到的，你可以给一个节点附加多个标签:

```
CREATE (:CABERNET_SAUVIGNON:RED) 
```

Enter fullscreen mode Exit fullscreen mode

### 创建一个带有标签和属性的节点

如果你还想给一个节点附加一些属性，你可以在括号内，在 JSON 样式的标签之后:

```
CREATE (:WINE {name:"Cabernet Sauvignon", fruity: false}) 
```

Enter fullscreen mode Exit fullscreen mode

用标签标记节点不是强制性的，但是如果你想附加一个标签，你必须在属性之前写它。

### 创建两个节点

只需用逗号分隔节点。CREATE 关键字只能使用一次。

```
CREATE (:WINE {name:"Cabernet Sauvignon", fruity: false}), (:WINE {name:"Merlot", fruity: true}) 
```

Enter fullscreen mode Exit fullscreen mode

## 创建新关系

您已经看到了创建新节点是多么容易。现在我们将看到创建两个节点并在它们之间建立关系是多么容易。

### 创建新节点间的新关系

```
CREATE (:GRAPE {name:"Cabernet Sauvignon"})-[:GROWS_AT]->(:SUBREGION {name:"VillÃ¡ny"}) 
```

Enter fullscreen mode Exit fullscreen mode

节点在括号中声明，而关系用括号表示。一个关系就像一个节点，可以有任意多的属性——同样，用 JSON 风格声明它们。然而，虽然一个节点可以有多个标签，但一个关系只能有一种类型。但是您可以在相同的两个节点之间添加不同类型的多个关系。

### 在现有节点之间创建新的关系

向已经存在的节点添加新的关系当然是可能的，但是这需要使用`MATCH`关键字。由于`MATCH`不是本文的一部分，我就不再赘述了。我可以将 Cypher 的`MATCH`与 SQL 的`FROM`和`WHERE`关键词联系起来。

```
MATCH (cabernet:Grape {name: 'Cabernet sauvignon'}), (villany:SUBREGION {name:"VillÃ¡ny"})
CREATE (cabernet)-[relationship_type:GROWS_AT]->(villany) 
```

Enter fullscreen mode Exit fullscreen mode

在这里你可以看到一些你以前没见过的东西。我用一个名字来定义节点和关系。您可以给它们命名并重用它们，也就是说，您可以拥有变量。如果我创建一个节点并将其命名为 cabernet，我可以在同一行中重用该节点，而无需再次给出完整的描述:

```
CREATE (cabernet:Grape {name: 'Cabernet sauvignon'}),
(villany:SUBREGION {name:"VillÃ¡ny"}),
(cabernet)-[relationship_type:GROWS_AT]->(villany) 
```

Enter fullscreen mode Exit fullscreen mode

事实上，如果您使用同一个声明两次(没有给新实体一个变量名)，两个节点/关系将被创建为具有相同的属性，但是具有不同的 id。

## 结论

今天我们已经看到了如何使用 CREATE 关键字来创建节点和/或关系。很快我们就会发现其他关键词，比如`MATCH`和`RETURN`。

## 行动号召

如果你对图形数据库感兴趣，去买一个。开始试验，例如 Neo4j。很容易[安装它](https://neo4j.com/docs/operations-manual/current/installation/)，但你也可以玩它[在线](http://console.neo4j.org/)。

如果你对未来感兴趣，请在推特上关注我。