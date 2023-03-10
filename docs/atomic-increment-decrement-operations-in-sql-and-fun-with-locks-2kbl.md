# SQL 中的原子递增/递减操作以及锁带来的乐趣

> 原文：<https://dev.to/pjam/atomic-increment-decrement-operations-in-sql-and-fun-with-locks-2kbl>

#### SQL 支持对数值列的原子递增和递减操作。“技巧”是使用一个更新查询，该查询遵循一个使用相对右侧值的特定模式。

[![](img/371e591ae8e18ac81914bfb372ed86da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UCXFLlkv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2ADZfjn33mWcGYNyq78LkVfg.jpeg)

我们最近重写了我们的库存管理系统，并通过利用本机 SQL 增量和减量操作来提高性能，同时降低操作复杂性。在本帖中，我们将深入探讨这些操作的细节和常见问题，并将我们的新实现与以前的实现进行比较，以突出优势。

SQL 支持对数字列的原子递增和递减操作。“诀窍”是使用基于以下模式的更新查询:

```
-- This assumes the existence of a table defined as:
-- CREATE TABLE test(id SERIAL PRIMARY KEY, x INTEGER);
UPDATE test set x = x - 1 where id = 1; 
```

Enter fullscreen mode Exit fullscreen mode

该查询中有两个重要元素:

*   WHERE 子句必须是确定性的(稍后的将详细介绍[)。](http://engineering.harrys.com/2017/06/28/atomic-operations-in-sql.html#condition)
*   update 语句的右边是使用相对值，而不是传递一个绝对的预选值(后面的中也有更多)。

PostgreSQL 文档有一个很好的例子。

### 死锁风险

请务必注意，由于更新查询将隐式使用行级锁，如果多个事务在隔离级别设置为 READ COMMITTED、REPEATABLE READ 或 SERIALIZABLE 的情况下运行，可能会发生死锁。

### 举例

让我们在测试表中插入两行。

```
INSERT INTO test VALUES (1, 0);
INSERT INTO test VALUES (2, 0); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以用两个 psql 会话触发死锁:

```
$1> psql
psql1> BEGIN;
psql1> UPDATE test SET x = x + 1 WHERE id = 1;
-- A lock is acquired on the row with id 1, no other transactions can update it

$2> psql
psql2> BEGIN;
psql2> UPDATE test SET x = x + 1 WHERE id = 2;
-- A lock is acquired on the row with id 2, no other transactions can update it

psql1> UPDATE test SET x = x + 1 WHERE id = 2;
-- The second session hasn't committed yet, this operation is now waiting

psql2> UPDATE test SET x = x + 1 WHERE id = 1;
-- The first session hasn't committed yet, this operation is now waiting 
```

Enter fullscreen mode Exit fullscreen mode

僵局！每个会话都在等待另一个会话提交或回滚:

```
ERROR: deadlock detected DETAIL: Process 14803 waits for ShareLock on transaction 43356; blocked by process 14431\. Process 14431 waits for ShareLock on transaction 43357; blocked by process 14803\. HINT: See server log for query details. CONTEXT: while updating tuple (0,1) in relation "test" 
```

Enter fullscreen mode Exit fullscreen mode

PostgreSQL 在几秒钟后自动检测到这种情况，并将自动回滚其中一个事务，允许另一个事务成功提交。

*注意:所有事务隔离级别都会出现这种情况*

### 解

防止这种情况的一种方法是在事务中更新多行时使用确定性排序，在这种情况下，如果两个事务都按照升序 id 对行进行排序，就不会出现任何死锁。

### 确定性条件

正如 PostgreSQL 文档中所解释的，使用 READ COMMITTED 隔离级别的事务使 increment 查询安全的原因是 UPDATE 查询的 WHERE 子句中使用的条件的确定性。

让我们看看使用一个不那么简单的查询会发生什么:

```
$1> psql
psql1> BEGIN TRANSACTION ISOLATION LEVEL READ COMMITTED;
psql1> UPDATE test SET x = x + 1 WHERE id = 2;
-- A lock is acquired on the row with id 2, no other transaction can update it

$2> psql
psql2> BEGIN TRANSACTION ISOLATION LEVEL READ COMMITTED;
psql2> UPDATE test set x = x + 1 WHERE x % 2 = 0;
-- A lock is acquired on all rows with an even x value, since there's a lock on the row with id 2, this query waits for the first transaction to commit or rollback

psql1> UPDATE test set x = x + 1 WHERE x % 2 = 0;
-- The second session hasn't committed yet, this query is now waiting as well 
```

Enter fullscreen mode Exit fullscreen mode

这造成了另一种死锁情况。

底线是:只要你在一个主键(或者一个不可变的列)上使用一个等式条件，那么就没有什么好担心的。如果你不…嗯，很难说会发生什么。

*注意:使用限制性隔离级别(如 REPEATABLE READ 或 SERIALIZABLE)实际上可能会使事情变得更复杂，因为应用程序代码需要用某种重试逻辑来处理序列化失败。在文章*底部的代码部分有例子

传递给 UPDATE 查询的新值不知道当前值是多少，这就是这个查询工作的原因，它只是将值递增(在获得行上的锁之后)到它加上或减去给定的差值。

如果我们首先读取值并使用它来计算新值，我们将需要依赖一个更复杂的锁定机制来确保在读取值之后和更新完成之前值不会改变。

### 真实世界的例子

电子商务公司通常会跟踪平台上销售的每个 SKU 的库存，一个简单的库存表可以定义为:

```
CREATE TABLE inventories(sku VARCHAR(3) PRIMARY KEY, quantity INTEGER); 
```

Enter fullscreen mode Exit fullscreen mode

我们库存系统的一个简化版本工作如下:

*   将所有 SKU 放入购物车
*   按字典顺序对 SKU 进行排序(以防止死锁)
*   发出类似“更新库存集数量=数量- x”的查询，其中 sku = y 返回数量，其中 x 是请求的数量，y 是实际的 sku 值。如果返回的数量太低，就会抛出一个错误，采购过程就会中止。

### 我们独创的(过度设计的)解决方案

几年前，当我们在 Harry's 编写我们的库存系统的第一个版本时，我们没有意识到我们只能依靠 SQL 来发布原子减量操作，我们最终使用了 Redis。

Redis 支持开箱即用的操作([INCR](https://redis.io/commands/incr)/[incr by](https://redis.io/commands/incrby)&[DECR](https://redis.io/commands/decr)/[decr by](https://redis.io/commands/decrby))，并且是单线程的，默认情况下不会暴露任何竞争条件。

这绝对是一个有效的实现(并且长期以来运行良好)，但是它给实现增加了大量的操作成本，因为库存数据“存在”在两个不同的数据存储中:Redis 和 PostgreSQL。

实现可以概括为:

*   我们需要减少 SKU x 的库存
*   是 Redis 中的值吗？
*   如果没有，从数据库中读取并在 Redis 中设置
*   以里兹语递减
*   检查新值，如果太低，则中止

### 示例代码

我用 Ruby 编写了一个[小型测试套件](https://gist.github.com/pjambet/2d1cbf68b0846a04302505367ce42a9e)(针对 MySQL 和 PostgreSQL)，突出了本文中提到的不同概念

*   相对更新查询的“安全性”,即使在读取未提交事务时也是如此
*   未提交读取和提交读取事务中的绝对更新问题
*   使用可重复读取或可序列化事务的示例，要求应用程序显式处理序列化错误的重试。

*原载于 2017 年 6 月 28 日*[*【engineering.harrys.com】*](http://engineering.harrys.com/2017/06/28/atomic-operations-in-sql.html)*。*

* * *