# 使用 T-SQL 取消数据透视

> 原文：<https://dev.to/collinbarrett/unpivoting-data-with-t-sql-2cbd>

[![Database Schema](img/316211facea21f94346f72e76d638f16.png)T2】](https://collinmbarrett.com/t-sql-unpivoting-data/)

## 要求

上周，一个业务应用程序用户向我提交了一个数据集(按州定价),我需要将它导入到应用程序的数据库中。用户提供了以下格式的 Excel 工作表:

| 库存单位 | 长吨 | 女士 | 阿肯色州 |
| --- | --- | --- | --- |
| one | .99 | .89 | .89 |
| Two | One point zero nine | .99 | .99 |
| three | .99 | One point zero nine | .99 |

然而，我的数据库表设计如下:

| 库存单位 | 状态 | 价格 |
| --- | --- | --- |
| four | 长吨 | .89 |
| four | 女士 | .99 |
| four | 阿肯色州 | One point zero nine |
| five | 长吨 | .89 |
| five | 阿肯色州 | One point zero nine |
| five | 女士 | .99 |

我需要一个计划来转换工作表数据以匹配我的表的结构。

## 一解

首先，我使用 [SSMS 导入和导出向导](https://docs.microsoft.com/en-us/sql/integration-services/import-export-data/import-and-export-data-with-the-sql-server-import-and-export-wizard)将 Excel 工作簿中的数据直接导入到一个新表中(`[tmpPrices]`)。

然后，我可以使用下面的查询从导入的表中选择 [`INSERT INTO SELECT`](https://www.w3schools.com/sql/sql_insert_into_select.asp) 。`UNPIVOT`操作符将`[State]`列标识符旋转成与特定的`[SKU]`对齐的行值。在`UNPIVOT`操作符(`[Price]`)之前指定的列是保存当前被旋转列下的值的列。将包含我旋转的列值的列跟在运算符(`[State]`)之后。

```
INSERT INTO [Prices]([Sku],[State],[Price])SELECT [SKU],[State],[Price]FROM(SELECT [SKU],[AR],[MS],[TN]FROM [tmpPrices]) p UNPIVOT([Price] FOR [State] IN([AR],[MS],[TN])) unpvt; 
```

*via [微软](https://docs.microsoft.com/en-us/previous-versions/sql/sql-server-2008-r2/ms177410(v=sql.105))T3】*