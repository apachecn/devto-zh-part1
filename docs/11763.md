# SQL INSERT with WHERE 子句

> 原文：<https://dev.to/adamkdean/sql-insert-with-where-clause-693>

所以我在这里，再一次，不得不面对 SQL。我不喜欢 SQL，它也不喜欢我。其中一个主要原因是，当我将一段格式良好的代码放入 Management Studio 时，它会对其进行严重破坏，就好像在说“这个 SQL 不仅不起作用，而且我已经把它嚼碎并吐回给你了。”

我目前正在编写一个 util 来从客户数据库中提取电子邮件地址，并生成 SQL 来将它们插入到我们的邮件列表数据库中。我不想冒险拥有两个电子邮件地址，因此可能会向客户发送垃圾邮件，我希望 get 一些 SQL，首先检查电子邮件是否存在于数据库中，如果不存在，则插入它。

经过大量可怕的小例子 SQL 和大约 45 个谷歌，我终于想出了一些工作代码！下面你会看到我首先检查一个 select 语句是否不存在。如果没有，我就运行插入代码。漂亮又简单，是吗...一旦完成并开始工作..

```
IF NOT EXISTS (SELECT EmailAddress FROM t_MailingList
 WHERE EmailAddress = 'test') 
BEGIN
 INSERT INTO t_MailingList
 (EmailAddress, AcceptsPostal, AcceptsEmail)
 VALUES ('test', 0, 1) 
END 
```

Enter fullscreen mode Exit fullscreen mode

页（page 的缩写）如果你像我一样遇到了复合错误，那么我建议要么 1)去问一个有经验的 sqler，2)保持冷静，或者我喜欢的方法 3)一个锤子。尽情享受吧！