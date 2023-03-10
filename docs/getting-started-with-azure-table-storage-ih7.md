# Azure 表存储入门

> 原文：<https://dev.to/funkysi1701/getting-started-with-azure-table-storage-ih7>

Azure 表存储是存储数据的廉价方式，但是它有一些你应该知道的缺点。

Azure 表存储是一种用键/属性对存储 NoSQL 数据的简单方法。我非常熟悉在 SQL 数据库中存储数据，仍然会选择 SQL 而不是表存储，但是表存储要便宜得多，所以可能值得根据您的项目进行调查。

[Troy Hunt](https://www.troyhunt.com/working-with-154-million-records-on/) 在他的《我被催眠了吗？所以有很多项目利用它来产生巨大的影响。

要使用表格存储，你需要使用一个 nuget 包。存储

**安装包 WindowsAzure。存储**

为了从 Azure Table storage 中的一个表加载数据，我使用了以下代码

。要点表{ margin-bottom:0；}

您需要创建一个从 TableEntity 派生的类(在上面的示例中称为 Entity ),该类定义了 Partitionkey 和 Rowkey，以及您希望存储在表存储中的其他列。行键和分区键惟一地标识了表中的数据，如果您习惯于 SQL，可以把它看作是表的主键。这个类还必须包含一个无参数的构造函数。

这是使用 partitionkey 和 rowkey 检索数据的唯一方法。如果您想要检索特定的数据，您需要检索所有行，然后在它们中搜索您需要的内容。对我来说，这不是一个大问题，因为我只有 150 行，但如果你有数百万行，你可能需要仔细考虑如何使用它。

为了保存数据，我使用了非常相似的代码

。要点表{ margin-bottom:0；}

我创建了一个实体对象，然后将它作为参数传递给 InsertOrReplace 方法。

删除数据也非常类似，创建一个实体对象并将其作为参数传递给 delete 方法。

在调试我的表存储代码时，我发现 [Azure Storage Explorer](https://azure.microsoft.com/en-gb/features/storage-explorer/) 对于查看表中实际存在的数据和可能抛出错误的内容非常有用，通常是我的实体有问题。

我之前提到过表存储比 SQL Azure 便宜。对于我的简单玩法，我发现我每月 10+的费用已经减少到 1+了。如果我要构建的东西不仅仅是学习它如何工作，我可能会继续使用 SQL，但对于学习新技术的成本来说，尝试一下表存储是非常值得的。

帖子[开始使用 Azure Table 存储](https://www.funkysi1701.com/2017/12/17/azure-table-storage/)首先出现在 [Funky Si 的技术演讲](https://www.funkysi1701.com)上。