# 为什么是 NoSQL，有哪些选择？

> 原文：<https://dev.to/kenwalger/why-nosql-and-what-are-some-of-the-options>

你有没有注意到开发人员有多局限于某项技术？Python 对 Java，Angular 对 React，Windows 对 Mac，猫对狗...所有的话题和主题都可以在几乎所有的讨论平台上引发一场精彩的“火焰战争”。唉，关系数据库(RDBMS)和非关系数据库也是如此。由于很大一部分 RDBMS 使用结构化查询语言(SQL)作为它们的语言，而非关系数据库，一般来说，不使用 SQL，所以争论被框定为 SQL vs. NoSQL。

SQL 和 RDMS 数据库设计已经存在很长时间了，并且是广泛使用的技术。事实上，它们是许多不同数据模型的优秀解决方案。您的所有数据都具有相同的属性吗？SQL 非常适合这一点。您正在处理复杂的事务性请求吗？SQL 在那个环境中大放异彩。

作为一个现实世界的例子，想想你的医生的办公室和他们办公室里所有整齐分类的文件。他们是否以与所有糖尿病诊断相关的方式将其存储在该文件中...癌症在这里，然后包括一个参考病人的联系信息，另一个文件为每个人的蓝十字保险，再次与更多的链接？这是 SQL 的方式——或者他们是否将某个病人的所有信息都存储在一个文件中？这是 NoSQL 数据模型的一个例子。

同样，在当今这个时代，数据来自各种设备，具有各种各样的数据类型，能够以更灵活的格式存储数据似乎很有意义。这种格式应该依赖于您的应用程序的访问模式，而不一定是严格格式化的数据模型，即**有**来适应现有的表结构。NoSQL 数据存储允许这种灵活性，并且通常允许更快的应用程序开发和迭代，因为设计的灵活性从模式本身一直延续到数据记录级别。

NoSQL 数据库有许多不同的格式，这并不奇怪。有键值存储选项如 [Redis](https://redis.io/) ，图模型如 [Neo4j](https://neo4j.com/) 或 [Giraph](http://giraph.apache.org/) ，列模型如 [Cassandra](http://cassandra.apache.org/) 和 [HBase](https://hbase.apache.org/) ，多模型选项如 [Couchbase](https://www.couchbase.com/) 或 [MarkLogic](http://www.marklogic.com/) ，文档数据模型如 [IBM Domino](http://www-03.ibm.com/software/products/en/ibmdomino) 和 [MongoDB](http://www.mongodb.com) 。是的，我知道，我在每个模型中遗漏了许多例子，让火焰战争开始吧...

这些不同的数据库模型各有什么优点和缺点？有几种不同的方法来评估每个数据库模型提供的各种功能，以确定它是否适合特定的组织或应用程序结构。我想看看两个更大的考虑因素，数据本身建模的方式和数据查询的方式。

### NoSQL 数据模型

#### 键值和列模型

如果我们看一下键-值模型如何存储它们的数据，这类似于列模型，我们会看到它们属于一个相当基本的模型类型。每个数据库项都存储为一个键或属性名，并与一个值相关联，或者在列模型的情况下，与一个多维排序映射相关联。这对于非结构化数据非常有用，因为数据库不需要跨键值对的模式集。由于设计的简单性以及数据库只对键感兴趣，这可以很好地扩展并具有高性能。

虽然这是一种快速表示数据的方法，但是这种设计中的值不能被查询，只有键可以。显然，这使得复杂的查询和聚合变得更加困难。仅通过单个键值查询数据的能力可能会受到限制。

#### 图形模型

图形模型使用节点、边和属性来表示数据。一个很好的例子是一个社交网络，其中每个人都是一个节点，他们与其他人的连接是边，属性是关于给定节点的信息，比如一个人的名字。这些数据库可能需要一点学习曲线才能理解，但对于业务供应链、社交网络或复杂的层次结构等通常难以在关系数据库中建模的事物来说，它们工作得很好。

然而，它往往是一种相当小众的数据存储，因为许多传统类型的信息并不适合图形数据存储的概念。当数据记录之间的*关系*是比实际数据本身更重要的概念时，它们表现得很好。探索你与他人之间有多少关系将是一个很好的用例，即“[凯文·贝肯](https://en.wikipedia.org/wiki/Six_Degrees_of_Kevin_Bacon)的 6 度”问题。

#### 文档模型

文档模型数据库将信息存储在文档中，而不是行和列。如果你错过了我关于用文档建模数据[的帖子，那会有更详细的内容。大多数情况下，数据存储在类似 JSON (JavaScript 对象符号)的结构中。这允许以大多数开发人员熟悉的方式存储数据，并允许每一段数据有效地成为一个对象。这是一个关键而重要的概念，与我们熟悉的面向对象编程模式紧密相关。](https://www.kenwalger.com/blog/nosql/modeling-your-data-with-documents/)

文档数据库中的数据可以具有动态模式，其中每个文档可以具有不同的字段，并且字段可以由不同的数据类型表示。这使得它非常有吸引力，因为在开发过程中很容易添加新的字段。这种灵活的动态模式能力通常可以弥补直接进行多记录事务和连接操作的不足。

### NoSQL 查询模型

由于每个应用程序都有自己的数据检索和存储要求，因此在决定如何*存储*您的数据时，确定*需要如何*检索您的数据是一个重要的考虑因素。一些应用程序可能有非常基本的查询需求。其他人可能有复杂的查询，在每个记录上搜索各种值。

如果我们回头看看我们的患者记录示例，医生办公室可能有一些只需要查找患者姓名的查询。然而，大多数时候，他们会查找额外的信息，如预约时间表、具有特定诊断的患者、特定的年龄范围等。拥有足够健壮的查询语言和数据库来处理这样的请求是非常重要的。此外，由于数据很少是静态的，并且经常需要更新，所以拥有基于一个或多个字段更新记录的能力是非常重要的。

#### 键值和列模型

如前一节所述，这些系统可以基于单个或有限数量的键来搜索和检索信息。对于更复杂的查询，用户经常被要求，事实上被鼓励，去开发和维护他们自己的索引。更新这些系统中的记录通常是昂贵的，需要多个步骤和对数据库的多次访问。事实上，无论更新的大小如何，通常都需要对记录进行整个重写。

#### 图形模型

正如您可以想象的，对于这些系统的需求，需要丰富的查询语言来探索简单和复杂的关系。查询语言需要能够提供关于数据的直接和间接假设。因此，关系风格分析在这里是最重要的，而其他通用应用程序则不太常用。

#### 文档模型

许多文档模型数据库的亮点之一是丰富的查询语言，允许对文档中的任何字段执行搜索。这通常包括向数据库添加辅助索引以进一步提高性能的能力。使用某种版本的 *find and modify* 方法，可以在一次数据库访问中频繁地完成数据更新。

### 外卖食品

在一天结束的时候，我们又该何去何从呢？我们如何决定使用哪个非关系数据库？首先，我建议看看您的具体应用需求。如果模式灵活性、快速开发和/或高效的数据查询很重要，NoSQL 绝对值得考虑。一旦做出选择，接下来就是选择哪种类型的非关系数据库。在许多情况下，文档模型拥有最广泛的、对开发人员最友好的选项集。

*在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我发布的最新消息，或者在我的[博客](http://www.kenwalger.com/blog)上查看原始帖子。*请告诉我您是如何使用 NoSQL 来满足您的应用需求的。