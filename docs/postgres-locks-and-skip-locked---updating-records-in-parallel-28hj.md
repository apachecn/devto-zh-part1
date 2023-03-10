# Postgres 锁和 SKIP 锁——并行更新记录

> 原文：<https://dev.to/tiagoamaro/postgres-locks-and-skip-locked---updating-records-in-parallel-28hj>

> 本文原载于我的[个人网站](https://www.tiagoamaro.com.br/2018/01/10/postgres-locks/)。

在本文中，我将展示我是如何解决以下挑战的:在使用 Postgres 的 locking 子句并行更新 Postgres 记录
时避免数据库死锁。

在我开发的一个应用程序中，我有如下的数据结构:一个有许多报价的产品，其中报价需要按每个产品的价格分组排序。按照这种逻辑，我想到了计算排名的可能方法:

1.  每当报价更新时计算排名
2.  在应用程序级别创建后台工作人员来处理报价排名更新
3.  将排名计算委托给数据库(使用触发器)

牢记[亲吻原则](https://en.wikipedia.org/wiki/KISS_principle)，我尝试了第一个实现。

## 第一种方法:通过回调更新排名

第一种方法很幼稚:创建一个操作，每当价格更新时，就更新报价的排名。如果您的操作是串行执行的，这种方式很好，但是如果您并行更新几个记录，您将面临数据库锁定系统的一些问题。

在我的例子中，我让 Puma 服务器执行多个调用来实时获取价格。

由于多个进程同时更新价格，更新报价使我陷入了一个死锁场景，
同一数据库行在不同的事务中被多次更新。在这种情况下，将 ranking
update 操作包装到一个事务中也是行不通的，因为多个事务仍然会陷入死锁。

由于这个场景看起来像一个“队列”，后台排队逻辑可以解决死锁问题。

## 第二种方法:背景工人

第二种方法甚至没有实现，因为后台工作人员应该总是并行执行，因为它的目标是
加速或推迟高成本的工作。

更新单个属性并不昂贵，它的串行执行特性才是真正的问题。

## 第三种方法:数据库触发器

因为死锁是问题所在，所以将问题推迟到数据库似乎是最合理的方法。

在 offers 表中使用触发器似乎是正确的方法，但是如果没有用正确的锁定机制选择记录
,触发器也会发生死锁。因此，我开始研究哪种机制是解决这个问题的正确方法。

浏览 Postgres 的文档， [`SKIP LOCKED`锁定子句](https://www.postgresql.org/docs/10/static/sql-select.html#SQL-FOR-UPDATE-SHARE)
似乎通过以下警告解决了这个问题:

> 锁定 SKIP 后，将跳过任何无法立即锁定的选定行。跳过锁定的行提供了一个
> 不一致的数据视图，所以这不适合一般用途的工作，但是可以用来避免多个消费者访问一个类似队列的表时的锁争用
> 。

这是一种可接受的折衷，因为报价排名是作为“类似队列的表”来更新的。

有了这个，我开始将我的更新操作移植到 Postgres 触发器。

## Postgres 实现

由于报价和产品之间存在“一对多”的关系，并且`product_id`是产品
表的外键，所以我编写了下面的触发器:

```
CREATE FUNCTION calculate_offer_product_ranking() RETURNS trigger
    LANGUAGE plpgsql
    AS $$
BEGIN
  WITH offer_scope AS (
      SELECT *
      FROM offers
      WHERE product_id = NEW.product_id
      FOR UPDATE SKIP LOCKED
  ), ranked_offers AS (
      SELECT
        id,
        row_number()
        OVER (
          PARTITION BY product_id
          ORDER BY CAST(price AS FLOAT) ) AS ranking
      FROM offer_scope
      WHERE offer_scope.product_id = NEW.product_id
  )

  UPDATE offers
  SET product_price_ranking = ranked_offers.ranking
  FROM ranked_offers
  WHERE offers.id = ranked_offers.id;

  RETURN NEW;
END;
$$;

CREATE TRIGGER calculate_offer_product_ranking_trigger AFTER INSERT OR UPDATE OF price ON offers FOR EACH ROW EXECUTE PROCEDURE calculate_offer_product_ranking(); 
```

Enter fullscreen mode Exit fullscreen mode

> 如果你不熟悉`WITH`子句，也不熟悉 Postgres 窗口函数(`PARTITION BY`)，我强烈推荐
> 阅读他们的文档，因为这些函数在数据分组时非常有用:
> 
> *   [`WITH`查询单据](https://www.postgresql.org/docs/current/static/queries-with.html)
> *   [窗口功能文档](https://www.postgresql.org/docs/current/static/tutorial-window.html)

## 结论

这个挑战提醒我，并不是所有的业务规则都会进入你的应用层，因为你的数据库是一个强大的
工具。数据库是令人难以置信的、可靠的和优化的软件，不仅仅是“存储数据”。

有了这个数据库触发器计算报价排名，我就可以保证排名，而无需在我的
应用层上创建任何变通办法来执行数据库固有的任务:原子性和一致性。