# 圣诞快乐

> 原文：<https://dev.to/funkysi1701/happy-christmas-5cfj>

我在推特上看到这条推文。

```
He’s making a database,
He’s filtering twice
SELECT * FROM customers WHERE behaviour = Nice
Santa Clause is Coming to town. 
```

Enter fullscreen mode Exit fullscreen mode

这让我开始思考，在一个规范化的数据库结构中，行为肯定不会存储在 customer 表中，所以我建议进行以下更改。

```
He’s making a database,
He’s filtering twice
SELECT * FROM customers WHERE EXISTS (select * from behaviour where behaviour.CustomerId = customers.Id and behaviour.Type = Nice)
Santa Clause is Coming to town. 
```

Enter fullscreen mode Exit fullscreen mode

大家圣诞快乐，希望你们都有一个宁静的假期。