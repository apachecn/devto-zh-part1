# Postgres 文本搜索:简单、足够

> 原文：<https://dev.to/_swanand/postgres-text-search-simple-adequate-2c8j>

在数据中搜索文本是一项经常被请求的功能，通常会带来出色的 UX。Gmail 的网络界面完全建立在搜索之上。难怪数据库长期以来一直支持基本的文本搜索操作符，如~、like、ILIKE 等。但是，当我们试图改进这个特性时，它们经常会出现问题或者给出不准确的结果。比方说，在多种语言中搜索，或者搜索同一个词的不同变体:考虑现实主义、现实主义和现实主义，或者[搜索一个词，但不搜索另一个](https://www.google.co.in/#q=ruby+-jewel)。

这就是全文搜索的用武之地。Postgres 提供了出色的全文搜索功能，使我们能够在应用程序中实现文本搜索，而不会产生额外的依赖性和操作开销。此外，内置的搜索允许我们将搜索与其他现有的查询和过程结合起来。当我说卓越的功能时，我指的是全功能。当我说全功能时，我的意思是它支持词干、搜索相关性、搜索高亮、模糊匹配和多种语言。

在这篇文章中，我们将看看基本的文本搜索。特别是，我们将看看文本搜索的基本组成部分，以及如何使用它们。

**文件**

这是我们要搜索的源数据。通常分布在多个列中。现在，我知道任何有价值的搜索都必须能够跨多个表中的多行进行搜索，但我们将在本文的稍后部分讨论这一点。Postgres 提供了一个函数`to_tsvector`来从文本中获取搜索文档。它接受文本作为参数，并返回文本的 tsvector。

```
 -- Example 1a: tsvector
    =# SELECT
       to_tsvector('With great power, comes great responsibility!')
       AS document;

                    document
    --------------------------------------------
     'come':4 'great':2,5 'power':3 'respons':6
    (1 row)

    -- Example 1b: have strings, will concat
    =# SELECT
       to_tsvector('With great power' || ' ' || 'comes great responsibility!')
       AS document;

                      document
    --------------------------------------------
     'come':4 'great':2,5 'power':3 'respons':6
    (1 row)

    -- Example 1c: have tsvectors, will concat
    =# SELECT
       to_tsvector('With great power')
       || to_tsvector('comes great responsibility!')
       AS document;

                      document
    --------------------------------------------
     'come':4 'great':2,5 'power':3 'respons':6
    (1 row) 
```

一些事情是值得注意的:

1.  标点符号没了，‘用’也没了。从文档中删除所有与搜索不相关的常见单词，如“with”。这些因语言而异，被称为“停用词”。
2.  单词被简化成基本形式。这些被称为“[词位](https://www.postgresql.org/docs/current/static/textsearch-intro.html)”；它们只不过是单词的标准化形式，这个术语来自[语言学](https://en.wikipedia.org/wiki/Lexeme)。
3.  词位按字母顺序排列，有与词位相关的数字。
4.  tsvectors 可以连接起来，*或*可以对连接起来的字符串进行操作

如果你想更深入地了解搜索实际上是如何工作的，这些是你可以读到的一些东西。现在，我们只关注一件事:to_tsvector 接受我们的文本输入，并返回一个可搜索的值。

顺便说一下，我真的很喜欢这种例子驱动的学习方法。代码和示例有助于更容易地理解主题，并为读者提供一个更深入挖掘的起点。本着这种精神，这篇博文是一篇 repl 驱动的博文。

**查询**

我们希望搜索的术语。通常是一个单词或短语，但也可以是任何文本。甚至是一份完整的文件。为了从给定的搜索字符串中获取查询对象，Postgres 有两个函数:`to_tsquery`和`plainto_tsquery`。`to_tsquery`允许我们使用通配符之类的控制字符，但是对输入的要求要严格得多。`plainto_tsquery`另一方面，它不使用控制字符，而是对所有输入进行转义，并且对 SQL 注入是安全的。在本文中，我们将只关注 to_tsquery，因为它符合我们的“全功能”要求。

```
 -- Example 2: tsquery
    =# SELECT to_tsquery('responsibility');
     to_tsquery
    ------------
     'respons'
    (1 row)

    -- Example 3: tsquery multiple words, with escaping
    =# SELECT to_tsquery('great\ responsibility');
         to_tsquery
    ---------------------
     'great' & 'respons'
    (1 row)

    -- Example 4: Wildcard search
    =# SELECT to_tsquery('Eliz:*');
     to_tsquery
    ------------
     'eliz':*
    (1 row)

    -- Example 5: Intersection (AND-ing)
    =# SELECT to_tsquery('Barry') && to_tsquery('Allen');
         ?column?
    -------------------
     'barri' & 'allen'
    (1 row)

    -- Example 5: Union (OR-ing)
    =# SELECT to_tsquery('Barry') || to_tsquery('Wally');
         ?column?
    -------------------
     'barri' | 'walli'
    (1 row) 
```

示例演示了 to_tsquery 的各种用法。通配符，AND 和 OR 完全按照你的期望去做。关键是，这些只是常规函数，像`LOWER`、`LENGTH`，所以我们可以在*任何*查询中使用它们。

```
 -- Example 6
=# SELECT
   name,
   to_tsvector(name) AS document
   FROM states
   ORDER BY name ASC
   LIMIT 10;

         name         |         document
----------------------+---------------------------
 Alabama              | 'alabama':1
 Alaska               | 'alaska':1
 Arizona              | 'arizona':1
 Arkansas             | 'arkansa':1
 California           | 'california':1
 Colorado             | 'colorado':1
 Connecticut          | 'connecticut':1
 Delaware             | 'delawar':1
 District of Columbia | 'columbia':3 'district':1
 Florida              | 'florida':1
(10 rows) 
```

**将文档和查询放在一起**

现在问题来了:如何实际使用 tsvector 和 tsquery？输入`@@`操作符。这是实际执行搜索的操作符。示例效果最好，所以让我们搜索一个包含单词“North”的美国州:

```
 =# SELECT
     name,
     to_tsvector(name) AS document
   FROM states
   WHERE to_tsvector(name) @@ to_tsquery('north')
   ORDER BY name ASC;

      name      |        document
----------------+------------------------
 North Carolina | 'carolina':2 'north':1
 North Dakota   | 'dakota':2 'north':1
(2 rows) 
```

或者，我们来搜索一个以“CA”开头的单词的州:

```
 =# SELECT
     name,
     to_tsvector(name) AS document
   FROM states
   WHERE to_tsvector(name) @@ to_tsquery('ca:*')
   ORDER BY name ASC;

      name      |        document
----------------+------------------------
 California     | 'california':1
 North Carolina | 'carolina':2 'north':1
 South Carolina | 'carolina':2 'south':1
(3 rows) 
```

这就是跨越表格多行的标准文本搜索。一个友好的，邻近的文本搜索只是一个 WHERE 子句！这里另一个明确的要点是，任何`@@`操作与您的普通`=`操作没有什么不同。自然，多表搜索只是一个连接。多列搜索只是一个连接。

然而，我喜欢这个想法的地方在于，我现在可以用现有的 SQL 查询组成这个搜索。假设我有一个关于“拥有 facebook 个人资料的用户的所有联系人”的查询，现在我可以在这个子集中进行“姓名搜索”。对我来说，这是内置搜索的最佳用例。可组合性是一种非常强大的设计模式。

关于`NULL` s 的一个注意事项:和 Postgres 中的所有东西一样，文本搜索不能很好地处理 NULLs。如果您有空列， [COALESCE](https://www.postgresql.org/docs/9.6/static/functions-conditional.html) 是您的朋友，请随意使用它！

**搜索相关性和排名**

搜索通常以查找“所有匹配的文档”为中心，而不是查找特定的文档。在最基本的形式中，搜索相关性归结为两个问题:

1.  我如何对搜索返回的结果进行排序？
2.  如何根据我的背景和需求控制排名？

第一个问题的答案是`ts_rank`函数。它接受 tsvector 和 tsquery 作为参数，并返回“rank”；这有点用词不当，因为与 1 比 2 比 3 好的常规排名不同，该排名具有“越高越好”的属性。最好用在 ORDER 子句中。下面是一个例子，搜索所有名字以“Eliz”开头的人。如果这听起来很奇怪，想想自动完成:

```
 =# SELECT
     first_name,
     ts_rank(to_tsvector(first_name), to_tsquery('eliz:*')) as rank
   FROM person_names
   WHERE to_tsvector(first_name) @@ to_tsquery('eliz:*')
   ORDER BY rank DESC
   LIMIT 10;

   first_name    |   rank
-----------------+-----------
 Elizabeth Eliza | 0.1215850
 Elizabeth       | 0.0607927
 Elizabeth       | 0.0607927
 Elizabeth       | 0.0607927
 Elizabeth       | 0.0607927
 Elizabeth       | 0.0607927
 Elizabeth       | 0.0607927
 Elizabeth       | 0.0607927
 ELIZABETH       | 0.0607927
 Elizabeth       | 0.0607927 
```

我想你会发现，绝对排名不如能够通过它来排序结果有用。这就引出了下一个问题，如何配置 ts_rank 函数？比方说，您正在搜索博客帖子，并且希望标题中的匹配比正文中的匹配更有分量。为此，Postgres 提供了“权重”。重量按优先顺序称为 A、B、C 和 D。这些权重的默认值为 1.0、0.4、0.2、0.1。这意味着与 A 匹配的权重是与 d 匹配的权重的 10 倍。

把这些权重想象成“标签”，即你把一个 tsvector 标记为 A 或 B 或 C 或 D，并指定哪些标签承载多少权重。这样，Postgres 将产生适当的排名。一旦我们看到 tsquery 是如何使用这些权重的，标签的类比就更有意义了。下面的查询演示了如何在 tsvector 中使用权重:

```
 =# SELECT ts_rank(
           setweight(to_tsvector('With great power'), 'A') ||
           setweight(to_tsvector('comes great responsibility!'), 'D'),
           to_tsquery('power'))
  AS rank;

   rank
----------
 0.607927
(1 row)

=# SELECT ts_rank(
           setweight(to_tsvector('With great power'), 'A') ||
           setweight(to_tsvector('comes great responsibility!'), 'D'),
           to_tsquery('responsibility'))
   AS rank;

   rank
----------
 0.0607927
(1 row) 
```

我们这里的文档由两部分组成，“拥有巨大的权力”，标记为 A，以及“承担巨大的责任”，标记为 d。

当搜索该文档时,“权力”的等级比“责任”高 10 倍，因为“权力”在组 A 中，而责任在组 d 中。如果没有权重，或者对所有组件具有相同的权重，它们将具有相同的等级。

权重 A = 1、B = 0.4、C = 0.2 和 D = 0.1 的默认分配可以改变。参考文档了解接受权重作为参数的`ts_rank`的变体。试验 psql 控制台中的值会让您知道什么最适合您。我经常发现默认值确实是最好的。

回到标签类比，这些相同的权重也可以分配给一个 tsquery 对象，然后查询将只在给定的权重组中匹配。相当像一个“过滤器”。这允许类似“匹配这个，但不匹配那个”的特性。看一看:

```
 =# SELECT setweight(to_tsvector('With great power'), 'A')
     || setweight(to_tsvector('comes great responsibility!'), 'A')
     @@
     to_tsquery('resp:*B')
  AS is_match;

 is_match
----------
 f
(1 row)

=# SELECT setweight(to_tsvector('With great power'), 'A')
     || setweight(to_tsvector('comes great responsibility!'), 'A')
     @@
     to_tsquery('resp:*A')
   AS is_match;

 is_match
----------
 t
(1 row) 
```

关于控制文本搜索的[官方文档](https://www.postgresql.org/docs/current/static/textsearch-controls.html)相当不错，也很详细。我强烈建议您至少阅读这部分文档，如果不是全部的话。

关于 Postgres 中基本全文搜索的帖子到此结束。祝你搜索愉快！一定要让我知道你用 tsquery 和 tsvectors 尝试了哪些奇怪的案例。

在后续文章中，我们将通过索引、模糊匹配、文本高亮显示和支持多种语言来提高搜索性能。