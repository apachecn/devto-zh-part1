# 什么是 SQL 注入，在围棋中如何避免？

> 原文：<https://dev.to/joncalhoun/what-is-sql-injection-and-how-do-i-avoid-it-in-go>

这篇文章摘自一篇关于用 Go 的数据库/sql 包将记录插入 SQL 数据库的文章[，但我也将它单独发表，因为我认为这是一个值得单独讨论的重要话题。如果你喜欢这篇文章，你可能会喜欢我的系列文章“](https://www.calhoun.io/inserting-records-into-a-postgresql-database-with-gos-database-sql-package/)[使用 PostgreSQL 和 Go](https://www.calhoun.io/using-postgresql-with-golang/) ”中的其他文章，其中我介绍了如何安装 PostgreSQL，如何单独使用它，然后我们开始使用它和 Go。

## 我假设你有基本的 SQL 知识

阅读本文时，我假设您对 SQL 有一些基本的经验，所以您可能知道类似下面的代码片段可以用来向 users 表中插入一个新的用户记录，将提供的每个值(`30`、`"jon@calhoun.io"`、`"Jonathan"`和`"Calhoun"`)分配给它们各自的列。

```
INSERT INTO users (age, email, first_name, last_name)
VALUES (30, 'jon@calhoun.io', 'Jonathan', 'Calhoun'); 
```

Enter fullscreen mode Exit fullscreen mode

类似地，您应该知道可以使用下面的 SQL 从同一个表中查找用户。

```
SELECT * FROM users WHERE email='jon@calhoun.io'; 
```

Enter fullscreen mode Exit fullscreen mode

本文的其余部分将集中在`SELECT`上，所以从技术上讲，你不必知道第一个做了什么来从这篇文章中获得价值，但它可能会有所帮助。

## 什么是 SQL 注入？

当像您这样的开发人员有足够的知识来创建如上所示的 SQL 查询时，就会发生 SQL 注入，并且会想，“我为什么不使用类似于`fmt.Sprintf()`的东西自己创建 SQL 语句呢？”。乍一看，这似乎完全可以接受。您正在创建的查询相对简单，所以当我们知道自己在做什么时，为什么还要学习一个全新的包呢？所以你开始编码，并想出一些类似下面的代码。

```
func buildSql(email string) string {
  return fmt.Sprintf("SELECT * FROM users WHERE email='%s';", email)
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然这一开始看起来没问题，但实际上有一些潜在的问题，而且是大问题。

这种方法的问题是，有数不清的边缘情况，你不太可能考虑，在某个时候或另一个会咬你。

这个问题不仅限于 SQL。当你使用像`fmt`这样的包创建字符串时，你正在使用一个完全不知道你的意图或者你正在修改字符串的上下文的包。当我们打印出日志语句并做相对简单的事情时，这通常是好的，但是当我们使用更复杂的技术如 SQL 或 HTML 时，这可能很快就会成为问题。

这很容易用一个例子来解释，所以让我们假设您想要使用`fmt`包重新创建上面的 SQL 语句，并且您得到了如下代码:

```
func insertQuery(email string) string {
  return fmt.Sprintf("SELECT * FROM users WHERE email='%s';", email)
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然这可能看起来适用于像`jon@calhoun.io`这样的电子邮件地址，但是如果用户试图使用像`'; DROP TABLE users;'`这样的电子邮件地址登录，会发生什么呢？去试试吧-【https://play.golang.org/p/v9qXpK4IrQ T2】

您应该会看到如下所示的 SQL 语句。

```
SELECT * FROM users WHERE email=''; DROP TABLE users;''; 
```

Enter fullscreen mode Exit fullscreen mode

看起来不错吧？不对！如果您仔细观察一下，就会发现这条语句不仅仅是查找用户，它还在末尾添加了一条额外的 SQL 语句，使**删除整个 users 表！**哎呀！这意味着，如果执行此操作，您将丢失所有用户数据。

这被称为 SQL 注入，当您让用户输入需要在 SQL 语句中使用的数据，并且不转义任何特殊字符，如单引号(`'`)字符时，就会发生这种情况。不幸的是，这可能是“黑客”试图攻击您的网站的最常见的方式之一，虽然一些 SQL 注入攻击可以用来获取数据，但它们中的大部分只会破坏您的大部分数据，留给您一个空的数据库，并向您的用户做大量的解释。

## 在围棋中我们如何避免这种情况？

通过使用`database/sql`包和参数占位符，我们能够构造正确自动转义的 SQL 语句。例如，您可能有一些大致如下的代码:

```
// This is for Postgres. Other SQL variants may use the ? character.
db.Exec("INSERT INTO users(name, email) VALUES($1, $2)", 
  "Jon Calhoun", "jon@calhoun.io") 
```

Enter fullscreen mode Exit fullscreen mode

这里的关键区别在于，我们并不试图自己构造 SQL 语句，而是提供可以轻松转义的参数。`database/sql`的底层驱动程序最终会知道它需要处理哪些特殊字符，并为我们避开它们，防止任何恶意的 SQL 运行。

这通过一个例子更容易理解，所以让我们回到上一个例子，我们想要使用电子邮件地址搜索用户。与其执行上面的危险 SQL，`database/sql`包(以及一个驱动程序)不如执行类似下面的 SQL。

```
SELECT * FROM users WHERE email='''; DROP TABLE users;'''; 
```

Enter fullscreen mode Exit fullscreen mode

虽然这可能看起来非常相似，但有一个非常重要的区别——电子邮件地址中的单引号是双重的，这是您在 SQL 中转义单引号字符的方式。这相当于在 Go 中的引号前加一个反斜杠。例如`fmt.Println("\"hi\", said the man")`将输出`"hi", said the man`，`'''; DROP TABLE users;'''`被视为 SQL 中的*字符串* `'; DROP TABLE users;'`，因此该语句将搜索电子邮件地址为`'; DROP TABLE users;'`的用户，而不是执行危险的`DROP TABLE users;`命令。这可能不会返回任何用户，但更重要的是它不会删除你的数据！

所以这个故事的简短版本是*总是使用`database/sql`包来构造 SQL 语句并将值插入其中* *。我保证，这会让你以后省去很多麻烦。

## 你喜欢这篇文章吗？加入我的邮件列表！

如果你喜欢这篇文章，请考虑将[加入我的邮件列表](https://www.calhoun.io/what-is-sql-injection-and-how-do-i-avoid-it-in-go/#subscribe)。

我大概每周会给你发一封邮件，让你知道我最近正在写的或者已经发表的新文章或者视频(比如这篇)。没有垃圾邮件。不要出售你的电子邮件。我会像对待自己的收件箱一样对待你的收件箱。

作为对您加入的特别感谢，我还将向您发送我即将开设的课程 [Web Development with Go](https://www.usegolang.com) 的截屏和电子书样本。