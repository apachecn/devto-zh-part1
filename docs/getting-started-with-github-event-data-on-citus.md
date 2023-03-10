# Citus 上的 GitHub 事件数据入门

> 原文：<https://dev.to/craigkerstiens/getting-started-with-github-event-data-on-citus>

获取示例模式和数据通常是测试数据库中比较耗时的部分之一。为了让您更容易理解，我们将用一个几乎任何开发人员都可以使用的开放数据集——github 事件数据来浏览 Citus。如果您已经有了自己想要测试的模式、数据和查询，请尽可能地使用它。如果您在设置方面需要任何帮助，请加入我们的 [Slack 频道](https://slack.citusdata.com)，我们将很乐意为您的数据讨论不同的数据建模选项。

### 模式和查询的概述

这里我们要使用的数据模型很简单，我们有`users`和`events`。事件可以是与组织相关的分叉或提交，当然还有更多。

首先，我们将登录到 [Citus Cloud](https://console.citusdata.com/users/sign_up) 并配置一个生产集群。*你完全可以使用每天只需花费 3 美元的开发计划，但在这种情况下，我们将使用生产实例，这样我们可以轻松地调整它的大小。一旦您配置了您的集群，您就可以使用您的标准 [Postgres psql](http://www.craigkerstiens.com/2013/02/13/How-I-Work-With-Postgres/) :
连接到它*

```
psql postgres://citus:3nHmf5NObkfOsKmvfni0Fg@c.fnq7xkf34cjb6vfdubz3cp6427a.db.citusdata.com:5432/citus?sslmode=require 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/ec55386ec24489ac7252313620ce3efe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--za6ePfJb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d3vv6lp55qjaqc.cloudfront.net/items/412o3i1S25441s0s3M02/Craigs_Github_Test___Citus_Console.png%3FX-CloudApp-Visitor-Id%3De4475d145dcf11ebcffabf840edcc11f%26v%3D7ee6cbe7)

现在我们要摆放两张桌子。

```
CREATE TABLE github_events                                                                   
(                                                                                            
    event_id bigint,                                                                         
    event_type text,                                                                         
    event_public boolean,                                                                    
    repo_id bigint,                                                                          
    payload jsonb,                                                                           
    repo jsonb,                                                                              
    user_id bigint,                                                                          
    org jsonb,                                                                               
    created_at timestamp                                                                     
);                                                                                           

CREATE TABLE github_users                                                                    
(                                                                                            
    user_id bigint,                                                                          
    url text,                                                                                
    login text,                                                                              
    avatar_url text,                                                                         
    gravatar_id text,                                                                        
    display_login text                                                                       
); 
```

Enter fullscreen mode Exit fullscreen mode

在事件的`payload`字段中，我们有一个 [JSONB](https://www.citusdata.com/blog/2016/07/14/choosing-nosql-hstore-json-jsonb/) 数据类型。JSONB 是 Postgres 中二进制形式的 JSON 数据类型。这使得在单个列中存储更灵活的模式变得容易，并且使用 Postgres，我们可以在这个列上创建一个`GIN`索引，它将索引其中的每个键和值。有了`GIN`索引，直接在有效载荷上用各种条件查询变得更快更容易。因此，在加载数据之前，我们将创建几个索引:

```
CREATE INDEX event_type_index ON github_events (event_type);                                                  
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将实际获取这些标准 Postgres 表，并告诉 Citus 将它们分割出来。为此，我们将为每个表运行一个查询。通过这个查询，我们将指定我们想要分片的表，以及我们想要分片的键。在这种情况下，我们将共享`user_id` :
上的事件和用户表

```
SELECT create_distributed_table('github_events', 'user_id');                                 
SELECT create_distributed_table('github_users', 'user_id'); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们准备加载一些数据。您可以下载两个示例文件 [users.csv](https://examples.citusdata.com/users.csv) 和 [events.csv](https://examples.citusdata.com/events.csv) 。我们还有一个 [large_events.csv](https://examples.citusdata.com/large_events.csv) 可用，尝试一下可能会更有趣，尽管下载和加载需要更长时间。下载完成后，连接 psql 并使用\copy:
加载数据

```
\copy github_events from events.csv CSV;
\copy github_users from users.csv CSV; 
```

Enter fullscreen mode Exit fullscreen mode

### 查询

现在我们已经为有趣的部分做好了准备，实际上是运行一些查询。让我们从最基本的开始。一个简单的`count (*)`来看看我们加载了多少数据:

```
SELECT count(*) from github_events;
 count
-------------
 126245
(1 row)

Time: 177.491 ms 
```

Enter fullscreen mode Exit fullscreen mode

所以一个简单的计数就可以了。我们稍后将回到这种聚合，但是现在让我们看几个其他的查询。在 JSONB `payload`列中，我们已经获得了一些数据，但是它会根据事件类型而变化。对于`PushEvent`类型，有一个与之相关的大小，包括每次推送中不同提交的数量。有了这个，我们可以执行类似于每小时提交的总数:

```
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
    FROM   github_events
    WHERE  event_type = 'PushEvent'
    GROUP BY hour
    ORDER BY hour;
        hour         | num_commits
--------------------------+-------------
 2016-12-01 05:00:00 |       22160
 2016-12-01 06:00:00 |       53562
 2016-12-01 07:00:00 |       46540
 2016-12-01 08:00:00 |       35002
(4 rows)

Time: 186.176 ms 
```

Enter fullscreen mode Exit fullscreen mode

但是我们也有我们的`users`桌。因为我们在同一个 id 上分片了用户和事件，这意味着数据是[共同位于](https://www.citusdata.com/blog/2016/12/22/scaling_out_sql_with-colocation/)一起的，并且可以很容易地连接起来。*在某些情况下，比如对于[多租户](https://www.citusdata.com/blog/2016/10/03/designing-your-saas-database-for-high-scalability/)数据模型，您会发现基于 tenant_id 的分片可以非常直接地进行横向扩展。*如果我们加入到`user_id`中，它应该会传递给所有分布的碎片，而不需要我们做任何额外的工作。我们可能想要做的事情的一个例子是，找到创建了最多存储库的用户:

```
SELECT login, count(*)
FROM github_events ge
JOIN github_users gu
ON ge.user_id = gu.user_id
WHERE event_type = 'CreateEvent' AND
payload @> '{"ref_type": "repository"}'
GROUP BY login
ORDER BY count(*) DESC;
             login              | count
--------------------------------------+-------
 atomist-test-web                |    60
 isisliu                         |    60
 atomist-web-test-staging        |    55
 direwolf-github                 |    50
 circle-api-test                 |    40
 uncoil                          |    23
 kvo91                           |    14
 ranasarikaya                    |    10
 Alexgallo91                     |     9
 marcvl                          |     9
 Joshua-Zheng                    |     8
 ... 
```

Enter fullscreen mode Exit fullscreen mode

### 缩放出去

Citus 的主要好处之一是，当您需要时，您可以横向扩展您的数据库，而不是纵向扩展。这意味着一个更水平的途径来扩展你的数据库，你不会碰到你能找到的最大实例的一些天花板。当然，相对于纵向扩展，横向扩展还有一些其他的好处。当你需要横向扩展时，在 Citus Cloud 上，只需进入设置并调整队形即可。一旦更改了大小并生效，您就需要重新平衡数据，使其分布在所有节点上。

首先让我们看看数据是如何驻留的:

```
SELECT nodename, count(*)
FROM pg_dist_shard_placement
GROUP BY nodename;
                 nodename                 | count
-----------------------------------------------+-------
 ec2-34-198-9-41.compute-1.amazonaws.com  |    32
 ec2-34-198-11-52.compute-1.amazonaws.com |    32
(2 rows)

Time: 83.659 ms 
```

Enter fullscreen mode Exit fullscreen mode

*如你所见，每个节点上有相同数量的碎片*

现在，让我们回到控制台的设置区域，调整集群的大小。要登录 Citus Cloud，请点击设置选项卡并点击调整大小。现在您将看到滑块，允许您调整集群的大小，缩放到您想要的大小，然后单击调整大小。几分钟后，您的所有节点都将可用。

[![](img/783ca12aff17b137da70429a780b470f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5Fe51duD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d3vv6lp55qjaqc.cloudfront.net/items/2b2f133M3o2u1B1f1s0L/resizer.gif%3FX-CloudApp-Visitor-Id%3De4475d145dcf11ebcffabf840edcc11f%26v%3Df0fb03bd)

**但是**，你的数据没有任何变化。要让新节点开始生效，您需要运行重新平衡程序。当您运行重新平衡器时，我们会将碎片从一个物理实例移动到另一个物理实例，以便您的数据分布更加均匀。发生这种情况时，写入会继续正常流动，并且写入会保留在协调器中。一旦操作完成，协调器上的这些写操作将继续进行。这意味着在整个操作过程中，没有延迟读取，也没有丢失写入。更好的是，对于多租户应用程序，我们将所有协同定位的碎片彼此协调移动，因此它们之间的连接继续按您预期的方式运行。

现在，让我们运行再平衡。当您运行它时，您将看到从一个节点移动到另一个节点的每个碎片的输出:

```
SELECT rebalance_table_shards('github_events', 0.0);
NOTICE:  00000: Moving shard 102072 from ec2-34-198-11-52.compute-1.amazonaws.com:5432 to ec2-34-197-198-188.compute-1.amazonaws.com:5432 ...
CONTEXT:  PL/pgSQL function rebalance_table_shards(regclass,real,integer,bigint[]) line 63 at RAISE
LOCATION:  exec_stmt_raise, pl_exec.c:3165
NOTICE:  00000: Moving shard 102073 from ec2-34-198-9-41.compute-1.amazonaws.com:5432 to ec2-34-197-247-111.compute-1.amazonaws.com:5432 ...
CONTEXT:  PL/pgSQL function rebalance_table_shards(regclass,real,integer,bigint[]) line 63 at RAISE
LOCATION:  exec_stmt_raise, pl_exec.c:3165
NOTICE:  00000: Moving shard 102074 from ec2-34-198-11-52.compute-1.amazonaws.com:5432 to ec2-34-197-198-188.compute-1.amazonaws.com:5432 ...
... 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以重新运行查询来显示所有的碎片位置，并查看新的碎片平均分布:

```
SELECT nodename, count(*)
FROM pg_dist_shard_placement
GROUP BY nodename;
                  nodename                  | count
-------------------------------------------------+-------
 ec2-34-197-198-188.compute-1.amazonaws.com |    16
 ec2-34-198-9-41.compute-1.amazonaws.com    |    16
 ec2-34-198-11-52.compute-1.amazonaws.com   |    16
 ec2-34-197-247-111.compute-1.amazonaws.com |    16
(4 rows) 
```

Enter fullscreen mode Exit fullscreen mode

这样我们也可以回到我们的`count (*)`查询。现在，我们已经将集群中的资源增加了一倍，可以并行执行的查询将会执行得更快。运行我们的基本`count (*)`查询，我们将看到现在的查询时间几乎是以前的一半:

```
SELECT count(*) from github_events;
 count
-------------
 126245
(1 row)

Time: 97.792 ms 
```

Enter fullscreen mode Exit fullscreen mode

### 今天就开始

如果你需要一个数据集来使用 Citus，我希望这能有所帮助。欢迎免费下载 Citus 和/或在 [Citus Cloud](https://console.citusdata.com/users/sign_up) 上注册并开始使用。

*[原载于 Citus 数据博客](https://www.citusdata.com/blog/2017/01/27/getting-started-with-github-events-data/)*