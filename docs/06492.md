# HMIS，R，SQL -工作挑战二

> 原文：<https://dev.to/ladvien/hmis-r-sql-work-challenge-two-20fi>

# 提供长期无家可归者名单

在这次工作挑战中，我们将利用从第一次挑战中学到的概念，并在此基础上继续努力。我们将结合来自 Client.csv 和 Enrollment.csv 的两个数据帧。然后，我们将应用 HUD 的公式获得长期无家可归者的姓名列表。

## 需要的数据

目前长期无家可归者的定义可以在住房与城市发展部的联邦登记册中找到:

> “长期无家可归”的个人被定义为居住在不适合人类居住的地方、安全避难所或紧急避难所的无家可归的残疾个人，或者居住在机构护理设施中，如果该个人在该设施中居住的时间少于 90 天，并且在进入机构护理设施之前一直居住在不适合人类居住的地方、安全避难所或紧急避难所。为了符合"长期无家可归者"的定义，该人还必须至少连续居住了 12 个月，或在过去 3 年中至少居住了 4 次，合计时间长度至少为 12 个月。每个时期必须包括至少 7 个晚上生活在一个不适合人类居住的地方、紧急避难所或安全避难所。

我们需要几个数据元素来计算某人是否长期无家可归。当客户进入一个程序时，这些数据元素被报告给案例经理并被输入到 HUD 入口评估中。

以下是我们将使用的数据元素列表:

1.  禁用条件
2.  过去三年
3.  过去三年
4.  DateToStreetESSH

上述所有数据元素都可以在 Enrollment.csv 中找到。因此，与上一个挑战类似，我们需要加入 Client.csv 和 Enrollment.csv。

我们已经介绍了如何使用连接将 CSV 中的所有数据放入一个数据帧中。这项挑战将建立在这种技能之上。这里的新概念将结合逻辑得到一个具体的答案。

*   [逻辑](https://en.wikipedia.org/wiki/Logical_connective)

在 SQL 中，我们将使用以下逻辑运算符:

*   **是(==)**
*   **不(！=)**
*   **和(& & )**
*   **或(||)**
*   **>(大于)**
*   **<(小于)**

例如，让我们用长期无家可归者的定义，用这些逻辑运算符把它变成计算机可以理解的东西。我们可以通过多次重写定义来做到这一点，每次都删除对人类有意义的内容，将有意义的内容留给计算机。

例如，这对大多数人类来说应该是有意义的。

> 长期无家可归者是残疾人，无家可归时间超过 364 天。或者，三年内残疾且无家可归三次以上，且无家可归的时间累计超过 364 天。

那一段好像有点难读吧？但是，人类应该能够理解它。现在，让我们看看强调逻辑运算符的同一段落。

> 一个长期无家可归的人**是**残疾人**和**无家可归**超过** 364 天。**或**，**为**残疾**，**三年内无家可归**超过**三次**，**无家可归时间合计**超过** 364 天。

这是计算思考者的技能，采用 HUD 提供的定义，并将其从人类可以理解的内容改写为计算机可以理解的内容。

下一步是用叫做[的伪代码](https://en.wikipedia.org/wiki/Pseudocode)重写该段落。

```
Chronic Homeless Individual == 

                    A person IS Disabled AND
                    A person > Homeless 364 days

                    OR

                    A person IS Disabled AND
                    A person homeless > 4 times AND
                    A person > 12 months homeless within 3 years 
```

Enter fullscreen mode Exit fullscreen mode

这有助于我们确保一切就绪，可以输入电脑。下一步将是实际编写 SQL 代码。

以下是让你长期无家可归的代码:

```
#############################################  ##### Get those with Disabling Condition ###  #############################################  disablingCondition  <-  sqldf("SELECT PersonalID 
                            FROM clientAndEnrollmentDf 
                            WHERE DisablingCondition = 1")  #############################################  ##### Length-of-Stay ########################  #############################################  # Participants who meet the length-of-stay in homelessness requirement  # Either through four or more occurences with cumulative duration exceeding a year  # Or a consequtive year.  #                 113 = "12 Months"  #                 114 = "More than 12 Months"  chronicityDf  <-  sqldf("SELECT PersonalID, 'Yes' As 'Meets LOS'
                               FROM activeEnrollment
                               WHERE (TimesHomelessPastThreeYears = 4
                                    AND (
                                          MonthsHomelessPastThreeYears = 113
                                          OR MonthsHomelessPastThreeYears = 114)
                                        )
                               OR (CAST(JULIANDAY('now') - JULIANDAY(DateToStreetESSH) AS Integer) > 364
                                   AND (DateToStreetESSH != '') 
                                  )
                               ")  #############################################  ##### Chronically Homeless ##################  #############################################  # Take the distinct PersonalIDs of individuals who meet both chronicity  # and disabling condition.  chronicallyHomeless  <-  sqldf("SELECT DISTINCT(a.PersonalID)
                              FROM chronicityDf a
                              INNER JOIN disablingCondition b
                              ON a.PersonalID=b.PersonalID
                             ") 
```

Enter fullscreen mode Exit fullscreen mode

这可能看起来令人不知所措，但这将是本周挑战的目的，证明这些代码在分解成基本部分时其实非常简单。

这才是真正的*课，每一个复杂的问题都可以通过一点一点地*变得极其*简单。计算思维的力量是非凡的。*

## 目标

我们将合并这两个数据集，并发现以下内容:

1.  长期无家可归者的名单。
2.  将此列表导出到 Excel 文档。

要获得此信息，我们需要执行以下操作:

1.  将 Client.csv 加载到数据框架 clientDf 中。
2.  将 Enrollment.csv 加载到数据框架 enrollmentDf 中。
3.  将 clientDf 内部联接到 enrollmentDf。
4.  计算某人是否长期无家可归。
5.  过滤那些长期无家可归的人。
6.  将个人姓名列表写入 Excel 文档。

## 资源

以下是对每个步骤都有帮助的资源:

### 第一步& 2

*   R 编程 A-Z -视频 41 -在 R 中加载和导入数据
*   R 编程 A-Z -视频 21-R 中的功能
*   [读写 R 中的 CSVs】](https://ladvien.com/hmis-sql-r-read-write-csv/)

### 第三步

*   完整的 SQL Bootcamp -视频#51 -内部连接概述
*   完整的 SQL Bootcamp -视频#52 -内部连接示例 [* HMIS，R，SQL -基础知识](https://ladvien.com/hmis-r-sql-query/)

### 步骤 4 & 5

*   [HMIS，R，SQL 查询](https://ladvien.com/hmis/hmis-r-sql-query/)
*   [HMIS 数据字典](https://www.hudexchange.info/resources/documents/HMIS-Data-Dictionary.pdf)
*   [使用布尔逻辑查询结果(所有部分和视频)](https://www.essentialsql.com/get-ready-to-learn-sql-4-query-results-using-boolean-logic/)

### 第六步

*   编写 Excel 工作簿-教程即将推出