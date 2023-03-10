# HMIS，R，SQL -工作挑战一

> 原文：<https://dev.to/ladvien/hmis-r-sql-work-challenge-one-38ik>

# 创建家庭暴力受害者列表

在第一个工作挑战中，您将使用 R 和 SQL 从 HMIS 数据提取(5.1)中获得家庭暴力受害者的按姓名排列的列表。

## 需要的数据

HMIS 的数据拉取仅仅是一个被分成多个 CSV 的关系数据库。这些 CSV 将按照 HUD 的规定改变格式。这些 CSV 的当前版本是 5.1。本次工作挑战的重点是两个 CSV。

1.  Client.csv
2.  HealthAndDV.csv

`Client`文件将包含每个客户端的一行，并且只有一行。每一行将包含大部分客户的人口统计信息。

对于参与者完成的每个 HUD 条目评估，`HealthAndDV`文件将包含一行。每个条目将包含一般健康信息以及客户的家庭暴力信息，这些信息是客户在 HUD 条目评估期间报告的。

将两个文件联系在一起的是`PersonalID`列。这个 ID 是所有客户 ID 中最重要的。长度为 32 个字符，包含数字和字母:

```
B7YIOJIGF9CDP6FV7TANQXLMQRMBTVTB 
```

Enter fullscreen mode Exit fullscreen mode

(注意，这个 ID 在我们的 HMIS 软件中称为“企业 ID”)

`Client`和`HealthAndDV`都包含`PersonalID`列。在处理关系数据库时，这通常被称为`key`。两个文件中的这个惟一 ID 将允许我们使用一种叫做**连接**的东西将两个数据集合并在一起。

现在，Client.csv 中的信息对我们人类来说是可读的。将会有`FirstName`、`LastName`、`SSN`等栏目。但是在 HealthAndDV.csv 中，信息更加复杂。在这个挑战中，我们将关注一个专栏`DomesticViolenceVictim`。当您打开数据集时，您可能会注意到，在这些列中，您将看到“1”或“0”，而不是“是”或“否”。这是因为计算机理解 1 和 0 比理解是或否要快得多。

这是管理数据的一个重要补充。让计算机更容易理解数据库，报告的生成将会更快。你必须考虑，如果使用 1 而不是 Yes 可以在计算中节省 0.5 秒，那么当你有一个包含 1000 条记录的数据集时，你就节省了 500 秒 8.3 秒。现在，乘以 170 万条记录。好吧，你明白了。

好的。回到手头的问题。只知道“1”等于“是”，“0”等于“否”。因此，在这个挑战中，我们希望找到所有在`DomesticViolenceVictim`列中有“1”的客户

## 目标

我们将合并这两个数据集，并发现以下内容:

1.  家庭暴力受害者的名单。
2.  统计有多少客户正在逃离家庭暴力。

实际上，第二条信息是计算受害者名单中有多少人。

要获得此信息，我们需要执行以下操作:

1.  加载 Client.csv 和 HealthAnd.csv
2.  根据列`DateCreated`将 HealthAndDV 数据集过滤为最新的
3.  在`PersonalID`相同的地方连接(合并)数据帧
4.  将合并的数据帧过滤给那些已经在`DomesticViolenceVictim`中报告了`1`的人
5.  将此数据写入文件。
6.  使用一个函数来计算这个受害者列表中有多少参与者。

## 资源

以下资源应该有助于您理解流程的每一步。

### 第一步

*   R 编程 A-Z -视频 41 -在 R 中加载和导入数据
*   R 编程 A-Z -视频 21-R 中的功能

### 第二步

*   完整的 SQL Bootcamp -第 5 部分中的所有视频
*   完整的 SQL Bootcamp -第 6 部分中的所有视频
*   [使用 SQLdf 中的日期](https://ladvien.com/sqldf-dates/)

### 第三步

*   完整的 SQL Bootcamp -第 8 部分中的所有视频

### 第 4 步-

*   [HMIS，R，SQL 查询](https://ladvien.com/hmis/hmis-r-sql-query/)

### 第五步-

*   [读取和写入 CSV](https://ladvien.com/hmis-sql-r-read-write-csv/)

### 第六步-

*   完整的 SQL Bootcamp -第 5 部分中的所有视频
*   完整的 SQL Bootcamp -第 6 部分中的所有视频