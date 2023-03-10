# 连接

> 原文：<https://dev.to/horia141/joins-4fop>

对于 web 程序员来说，连接是一个长期的痛苦来源，尤其是那些不太直接与数据库交互的程序员。然而，它们是不可或缺的强大工具。

在标准设置中，连接被用来做一些非常简单的事情，这可以被最好地描述为“获取与我已经拥有的数据相关的其他数据”。我相信这也是最常见的用例，遗憾的是它没有被更多地放在最前面。

假设你有一个书店数据库，大致如下:

```
create table Book (
  id int,
  name varchar
);

create table Review (
  id int,
  book_id int foreing key (Book.id),
  text varchar
); 
```

如果你想检索某本书，你可以这样写:

```
book = db.query('select * from Book where id=:id', id=10) 
```

如果你想检索这本书和它的评论，一个自然的方法是这样写:

```
book = db.query('select * from Book where id=:id', id=10)
book.reviews = db.query('select * from Review where book_id=:book_id', book_id=book.id) 
```

当性能不成问题时，这是一段很好的代码。事实上，这是你为没有外键关系的数据库编写的那种代码，比如许多 NoSQL 数据库。然而，代码是非常低效的。您需要两个查询来检索数据，这意味着两个连接和所有各种额外的带宽，并增加了最终用户的延迟。此外，数据库没有机会整体优化查询。对于这个问题，它能做的不多，但是对于更复杂的查询，它确实很重要。

然而，也许最重要的问题是我们查询的复杂性是有限的。假设我们想要 2016 年写的所有书及其评论。代码可能类似于:

```
books = db.query('select * from Book where year_published=:year', year=2016)
reviews = db.query('select * from Review where book_id in :book_ids', book_ids=[b.id for b in books])
# Match reviews to books
for r in reviews:
  books[r.book_id] = r 
```

我们有大量的书籍从数据库中传输。然后发送第二个非常大的查询。最后，检索评论，并且必须手动将其与书籍进行匹配。

如果我们想要检索`Authors`、`Critics`或类似的东西，事情会变得更加复杂，性能分析会变成垃圾。

答案当然是加入。第一个查询可以写成:

```
book_with_reviews = db.query('''
    select b.*, r.*
    from Book as b
    join Reviews as r
    on b.id = r.book_id
    where b.id = :id
    ''', id=10) 
```

从概念上讲，这个查询与上面的查询做的是一样的事情，只是图书与其评论的匹配发生在数据库端，并考虑了过滤。有了合适的索引，相同的实体被访问，但是只需要一个请求。然而，由于关系表示的局限性，返回的结果并不是最直观的。这是一个表，其中串联了图书中的所有列和评论中的所有列，并且图书的行在每个评论中重复出现。

然而，当要检索所有书籍和所有评论时，情况就简单多了。该查询类似于:

```
book_with_reviews = db.query('''
    select b.*, r.*
    from Book as b
    join Reviews as r
    on b.id = r.book_id
    ''', id=10) 
```

这很容易扩展到越来越复杂的查询。同样，只需要发出一个请求，数据库就可以随意优化查询:

```
super_books = db.query('''
    select b.*, r.*, a.*, e.*
    from Book as b
    join Reviews as r
    on b.id = r.book_id
    join Author as a
    on b.author_id = a.id
    join Edition as e
    on e.book_id = b.id
    where e.year > 2000
    ''', id=10) 
```

到目前为止，我们看到的连接都是`inner joins`的例子。这里，为了发送一行，必须存在匹配的书和评论。这并不完全是我们想要的，因为书籍很可能没有评论。一个`left join`总是包含来自第一个表的实体，并在没有匹配的地方为第二个实体发出`nulls`。它是为示例产生正确结果的那个。一个`right join`总是包含来自第二个表的实体，并在没有匹配的地方为第一个实体发出`nulls`。最后，`full joins`包含第一个和第二个表中的所有实体，并尽可能地匹配它们。

joins 比`select`和`where`更难理解的原因之一是程序员在第一次接触 SQL 时通常没有一个关于它们的心理模型。虽然过滤和映射对于大多数人来说是根深蒂固的数据处理模式，但是很少遇到连接。当然，内存中可统一访问的对象的层次结构是一个比关系模型更有表现力的模型，所以它没有任何*需求*。

然而，最近几年，joins 已经作为一等公民出现在编程语言的标准库中，与`map`、`filter`和`reduce`并列。C#的 [LINQ](https://msdn.microsoft.com/en-us/library/bb308959.aspx) 和[并行 LINQ](https://msdn.microsoft.com/en-us/library/dd460688(v=vs.110).aspx) 就是一个例子。特别是对于计算密集型任务，用 LINQ 来表达一切，作为一种关系型转换，比打破`for`循环要优雅得多。其他有趣的例子是 Google 的 [FlumeJava](https://research.google.com/pubs/pub35650.html) 系统和 [Apache Spark](http://spark.apache.org/) ，其中使用类似的语法描述分布式数据处理管道。