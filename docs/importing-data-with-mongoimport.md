# 使用 mongoimport 导入数据

> 原文：<https://dev.to/kenwalger/importing-data-with-mongoimport>

在几乎每个人的数据库体验中，都有这样一个时期，从外部来源引入数据是非常好的。数据通常是电子表格格式(CSV 或 TSV)或者 JSON 格式。我在[的上一篇文章](https://www.kenwalger.com/blog/nosql/mongodb-cli-tools/)中讨论了一些 MongoDB 提供的命令行工具。使用 CLI 工具 mongoimport 可以轻松地将数据导入到 MongoDB 数据库中。

对于许多用例来说，mongoimport 非常简单。事实上，它在 MongoDB 大学的课程中被大量使用，例如作为一种快速填充数据库的方法。然而，除了简单地填充一个空的[集合](https://docs.mongodb.com/manual/core/databases-and-collections/)，我还想看一些用例。

### 蒙古进口

##### 连接

mongoimport 将连接到一个正在运行的 [mongod](https://docs.mongodb.com/manual/reference/program/mongod/) 或 [mongos](https://docs.mongodb.com/manual/reference/program/mongos/) ，默认情况下，实例运行在本地主机的端口 27017 上。mongoimport 命令的语法相当简单。例如，如果我们想用一个`posts.json`文件填充`blog`数据库中的`posts`集合，那么运行下面的命令就足够简单了。

```
mongoimport --db blog --collection posts --file posts.json

```

这很简单。通过使用这些标志的简写版本，我们也可以使它变得更容易。

```
mongoimport -d blog -c posts --file posts.json

```

如果我们想确保我们的`posts`集合被丢弃，只有新的数据在那里，我们可以使用`--drop`标志。

```
mongoimport -d blog -c posts --drop --file posts.json

```

如果您需要更改主机或端口号，也有相应的标志，分别是`--host`和`--port`。`--host`甚至更方便，因为它允许您在末尾添加端口，并使用更短的标志`-h`。所以下面是一样的:

```
mongoimport --host 123.123.123.1 --port 1234 -d blog -c posts --file posts.json

```

```
mongoimport -h 123.123.123.1:1234 -d blog -c posts --file posts.json

```

这也很简单。然而，如果我们的 MongoDB 服务器像任何好的服务器一样需要用户认证，那该怎么办呢？

##### 服务器认证

[数据安全](https://www.amazon.com/gp/product/1491937017/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=1491937017&linkCode=as2&tag=kenwalgersite-20&linkId=b9276793e1f3917877feace05ed4e90f) [![](img/e1d026ed85697006d1e8e338e69e2cc6.png)](//ir-na.amazon-adsystem.com/e/ir?t=kenwalgersite-20&l=am2&o=1&a=1491937017) 说到服务器管理，每个人都应该想到这一点。考虑到这一点，MongoDB 提供了多种方法来保护您的数据。假设一个人需要获得对服务器的认证访问，如何使用 mongoimport 来做到这一点呢？你猜对了，也有相应的标志。`--username`或`-u`和`--password`或`-p`是你的朋友。

```
mongoimport -h 123.123.123.1:1234 -u user -p "pass" -d blog -c posts --file posts.json

```

我们可以通过去掉`--password`标志来增加一些额外的保证，mongoimport 会提示输入一个合适的密码。

对于一些简单的认证选项来说，这很好，但是如果我们有一个更复杂的认证系统，有一个[认证数据库](https://docs.mongodb.com/manual/core/security-users/#user-authentication-database)，会怎么样呢？我们可以用`--authenticationDatabase`标志指定一个。这对于防止只有经过授权的人才能将数据导入您的集合非常方便。

mongoimport 为连接到安全服务器提供了大量的标志选项。我强烈建议根据您的环境查看[文档](https://docs.mongodb.com/manual/reference/program/mongoimport/)中的细节。

### 文件和列类型

如前所述，mongoimport 处理 CSV、TSV 和 JSON 文档。默认情况下，导入格式是 JSON。使用`--type`标志，我们可以导入 CSV 或 TSV 文件。因为 CSV 和 TSV 文件可以包含一些特殊的特性，所以让我们看看使用它们和 mongoimport 的一些选项。

很多时候，CSV 或 TSV 文件会包含标题行。如果我们可以在 MongoDB [文档](https://docs.mongodb.com/manual/core/document/)中使用这些头值作为字段名，那会很方便，对吗？那么，mongoimport 提供了一个`--headerline`标志来为我们实现这一点。

如果我们的 CSV 或 TSV 文件*不包含标题信息，mongoimport 也有一个解决方案。使用`--fields`标志，可以提供一个逗号分隔的字段名列表。或者，您可以生成一个字段名文件，每行一个名称，并将其与`--fieldFile`标志一起传递。*

除了 MongoDB 版新增的一些[其他特性](https://www.kenwalger.com/blog/nosql/new-version-new-features/)之外，mongoimport 还增加了一些新特性。其中之一就是
`--columnsHaveTypes`旗。当与`--fields`、`--fieldFile`或`--headerline`标志一起使用时，它允许您指定每个字段的类型。您以`columnName.type()`的格式将字段名连同任何参数传递给`type()`方法。因此，举例来说，如果你正在键入一个名为`isAdmin`的文件，你将使用`isAdmin.bool()`，看看`--columnsHaveTypes` [文档](https://docs.mongodb.com/manual/reference/program/mongoimport/#cmdoption-columnshavetypes)中可用类型和支持参数的列表。

这里有一个公平的警告，处理头信息的标志是针对 CSV 和/或 TSV 文件的。如果有人试图将它们用于 JSON 格式的文件，mongoimport 就会变得暴躁并返回一个错误。

### 导入到现有集合中

我想介绍的最后一个概念和标志列表是针对那些希望将数据导入到现有集合中的情况。
`--mode`标志提供了一种方法来告诉 mongoimport 如何处理与传入文档匹配的现有集合文档。`--mode`标志有三个选项，**插入**，**向上插入**，以及**合并**。

*   Insert 允许将文档放入集合中，唯一的检查是具有惟一索引的字段。如果有重复的值，mongoimport 会记录一个错误。
*   Upsert 用导入文件中的*新*文档替换数据库中的文档。所有其他文档都会被插入。
*   合并，嗯，它将现有文档与匹配的传入文档合并，并插入其他文档。这是 3.4 版本的另一个新特性。

如果您需要以其中一种方式导入文档，请查看[文档](https://docs.mongodb.com/manual/reference/program/mongoimport/#merge-matching-documents-during-import),了解基于除`_id`之外的字段进行向上插入和合并的选项。

### 包裹

MongoDB 还提供了一个类似但相反的函数 mongoexport。虽然这两个工具都很强大，但是它们并没有提供 MongoDB 所使用的 BSON 数据类型。因此，这些工具应该**而不是**用于生产备份。MongoDB 为备份方法提供了[其他工具](https://docs.mongodb.com/manual/core/backups/)。

我希望这篇文章能让你对 MongoDB 包组件工具有所了解，它是“开箱即用”的。一些编程语言开发了自己的导入数据的独立工具。其中一些比另一些更好。对我来说，既然已经提供了如此强大的导入工具，我发现自己更多的时候使用 mongoimport。

如果您自己还没有尝试过，我会鼓励您这样做。

这篇文章中有几个 MongoDB 特定的术语。我为[亚马逊 Echo](https://www.amazon.com/gp/product/B01DFKC2SO/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01DFKC2SO&linkCode=as2&tag=kenwalgersite-20&linkId=f9e513223de2525a72b95cf9561db55b) 系列产品创建了一个 [MongoDB 字典](https://www.echoskillstore.com/MongoDB-Dictionary/45103)技能。检查一下你可以说“Alexa，问一下 MongoDB 关于认证的定义？”并得到有益的回应。

* * *

*在 Twitter [@kenwalger](https://www.twitter.com/kenwalger) 上关注我，获取我发布的最新消息，或者在我的[博客](https://www.kenwalger.com/blog)上查看原始帖子。*