# HMIS、R 和 SQL -基础知识

> 原文：<https://dev.to/ladvien/hmis-r-and-sql-basics-25e1>

## 黑客游戏攻略

[![](img/300ccfda6b78de037a7b29190846b017.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tcrb67MP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/Screenshot_2016-12-27_07.39.30.png)

我是一个*黑客*。如果您发现错误，请在下面留下评论。如果你有意见，我会听听，但如果没有争论，我通常不会同意。

### 联接(合并数据)

R 和 SQL 最好的部分可能是它们能够围绕一个键快速组合数据。例如，在 HMIS CSV 中，Client.csv 包含大量人口统计信息，而 Enrollment.csv 包含大量评估信息。由于退伍军人信息在 Client.csv 中，而残疾信息在 Enrollment.csv 中，因此当需要计算退伍军人和残疾人员的总参与人数时会很困难。

连接是一个非常宽泛的话题；我不会试图涵盖他们所有的怪癖，但这里有一些我觉得有用的视频:

对于 HMIS 数据，两种有用的连接是左连接和内连接。左连接保留左表中的所有数据和右表中匹配的数据，内连接只保留匹配的数据。

以下是 Client.csv 和 Enrollment.csv 上下文中的一个示例:

Client.csv

| 个人 id | 西方人名的第一个字 | 老兵身份 |
| one two three four five | 简（女子名） | 是 |
| Fifty-four thousand three hundred and twenty-one | 乔（人名） | 不 |

注册. csv

| 个人 id | 西方人名的第一个字 | 禁用条件 |
| one two three four five | 简（女子名） | 是 |
| Fifty-four thousand three hundred and twenty-one | 乔（人名） | 不 |
| Forty-five thousand three hundred and twenty-one | 斯文 | 是 |

下面是两个 join 语句及其对上述数据的结果

```
SELECT * 
   FROM client a 
   LEFT JOIN enrollment b ON a.Personal=b.PersonalID 
```

Enter fullscreen mode Exit fullscreen mode

这种连接应该产生以下结果:

| 个人 id | 西方人名的第一个字 | 老兵身份 | 禁用条件 |
| one two three four five | 简（女子名） | 是 | 是 |
| Fifty-four thousand three hundred and twenty-one | 乔（人名） | 不 | 不 |
| Forty-five thousand three hundred and twenty-one | 斯文 | 空 | 是 |

注意斯文被保留了，即使他在连接后没有条目 Client.csv，因为他没有

并且内部连接看起来像这样:

```
SELECT * 
       FROM client a 
       INNER JOIN enrollment b ON a.Personal=b.PersonalID 
```

Enter fullscreen mode Exit fullscreen mode

这种连接应该产生以下结果:

| 个人 id | 西方人名的第一个字 | 老兵身份 | 禁用条件 |
| one two three four five | 简（女子名） | 是 | 是 |
| Fifty-four thousand three hundred and twenty-one | 乔（人名） | 不 | 不 |

### 计数

```
PersonalID  <-  sqldf("SELECT DISTINCT PersonalID FROM client") 
```

Enter fullscreen mode Exit fullscreen mode

上面的方法创建了一个来自 Client.csv 的客户端数据帧中所有 PersonalIDs 的向量。简而言之，它创建一个重复参与者列表。

举个例子，

| PersonalID | 其他数据 |
| one two three four five | xxxxxxxxx |
| Fifty-six thousand eight hundred and thirty-nine | xxxxxxxxx |
| one two three four five | xxxxxxxxx |
| Thirty-two thousand four hundred and fifty-three | xxxxxxxxx |

应该会产生以下结果，

| 个人 id |
| one two three four five |
| Fifty-six thousand eight hundred and thirty-nine |
| Thirty-two thousand four hundred and fifty-three |

这在创建关键向量时很有用，因为其他 CSV 与 PersonalID 有一对多的关系。举个例子，

Enrollment.csv 看起来像这样

| 个人 id | ProjectEntryID | 进来吧 |
| one two three four five | Thirty-four thousand five hundred and twenty-three | 2016-12-01 |
| Fifty-six thousand eight hundred and thirty-nine | Twenty-four thousand five hundred and twenty-three | 2015-09-23 |
| one two three four five | Twenty-three thousand four hundred and forty-three | 2014-01-10 |
| Thirty-two thousand four hundred and fifty-three | Thirty-two thousand four hundred and fifty-four | 2015-12-30 |

这反映了一个客户(即 12345)两次进入一个项目，一次是在 2014-01-10，另一次是在 2016-12-01。

### 总参与人数:

```
SELECT COUNT(PersonalID) as 'Total Participants' FROM client 
```

Enter fullscreen mode Exit fullscreen mode

这个查询应该给出一个行输出，计算数据帧中的客户端数量。

| 参与者总数 |
| one | One thousand six hundred and nine |

但是，如果有重复的 PersonalIDs，它会将每个条目都计为一个 ID。要获得数据帧中唯一客户端的计数，请添加 DISTINCT 命令。

```
SELECT COUNT(DISTINCT(PersonalID)) as 'Unique Total Participants' FROM client 
```

Enter fullscreen mode Exit fullscreen mode

### 条件数据

通常在 HMIS 数据中，有必要找到满足特定要求的参与者集合。例如，“这个数据集中有多少人是残疾人？”这就是 where 语句非常有用的地方。

```
SELECT PersonlID FROM clientAndEnrollment WHERE disability = 'Yes' 
```

Enter fullscreen mode Exit fullscreen mode

该语句将返回一个向量，其中包含声明自己被禁用的所有参与者的 PersonalID。可以使用 total participant 查询，但是有一个替代方法。

```
SELECT SUM(CASE WHEN 
               disability = 'Yes' THEN 1 ELSE 0 
           END) as DisabledCount 
```

Enter fullscreen mode Exit fullscreen mode

上面的语句使用了 CASE WHEN END 语句，我理解为 SQL 版本的 IF 语句。这里是 C 的等价物:

```
for(int i = 0; i < total_participants; i++)
    if(disability == true){
       disabilityCounter++;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### BOOL!

布尔运算符可以用来获得更复杂的条件数据:

```
SELECT PersonalID FROM clientAndEnrollment 
       WHERE disability = 'Yes' 
       AND gender = 'Female' 
```

Enter fullscreen mode Exit fullscreen mode

该语句将为残疾和女性客户提供所有 PersonalID 的向量。

好了，现在就到此为止吧。