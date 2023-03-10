# 使用 PostgreSQL 的统一多租户用户缓存

> 原文：<https://dev.to/dmfay/a-unified-multi-tenant-user-cache-with-postgresql-6o2>

我一直在开发一个多租户 Node.js 产品，该产品最近将其身份验证转移到了单点登录(SSO)系统中。使用 PostgreSQL，我们能够通过不常见或独特的数据库功能的有趣组合来高效地构建和检索用户数据:

*   外部数据包装器(fdw)
*   表继承
*   物化视图

## 外来数据包装器

当我们开始重新设计应用程序的用户基础设施时，我们希望避免维护独立于所选 SSO 系统的用户数据副本。我们知道我们可以通过外部数据包装器表示来自其他来源的数据。据我所知，这是 Postgres 的一个独特特性，它允许您通过实现一个标准的连接 API 将其他来源的数据表示为表格。

坏消息是:Postgres 是用 C 写的，虽然我可能会温习指针并最终使它工作，但高级语言已经宠坏了我。幸运的是，有一个项目可以用 Python 实现 FDW 开发: [Multicorn](http://multicorn.org/) 。在我的同事对 [foreign-keycloak-wrapper](https://github.com/schne324/foreign-keycloak-wrapper) 的努力下，我们已经能够在 keycloak 中创建一个代表特定“领域”或用户组织的表(我们保留了我们的`organizations`表，以便在我们的数据所有权中具有引用完整性),并通过 Keycloak API 检索它的用户。

```
CREATE SERVER "myrealm_server"
FOREIGN DATA WRAPPER multicorn
OPTIONS (
  wrapper 'keycloak.Keycloak',
  url 'url to the Keycloak instance',
  username 'a realm admin username',
  password 'a realm admin password',
  realm 'myrealm',
  client_id 'a realm client id',
  grant_type 'password',
  client_secret 'a realm client secret',
  organization_id 'id of an entry in the organizations table'
);

CREATE FOREIGN TABLE "myrealm" (
  id uuid,
  username text,
  "firstName" text,
  "lastName" text,
  email text,
  organization_id uuid
) SERVER "myrealm_server";

SELECT * FROM myrealm; 
```

Enter fullscreen mode Exit fullscreen mode

我们仍然需要为每个领域创建一个外部服务器和表，因为 Keycloak API 按照设计只检索每个领域的用户。在一个多租户系统中，我们希望在后端引导新的组织变得容易和自动化。在这里，Keycloak 能够将领域连接信息导出为 JSON，这让我们可以动态地访问`CREATE FOREIGN SERVER`和`CREATE FOREIGN TABLE`所需的信息。因此，虽然在创建后可以从新领域提取用户信息，但它总是被领域分开。我们不想在 JavaScript API 中找出从哪个表中提取数据——最好尽可能简单明了，并管理数据库中的数据复杂性。这就是它存在的意义。

## 表继承

表继承是四大 RDBMSs 中 Postgres 独有的另一个特性。建立一个基础`users`表并声明`myrealm`表`INHERITS (users)`完成两件事:

首先，`myrealm`构建在`users`列列表的顶部。这主要使`CREATE FOREIGN TABLE`语句更短(也是可选的)，因为只要基本`users`模式符合 Keycloak API 契约，我们就没有新的列要添加。

二、`myrealm`的数据可以通过简单的`SELECT`从*到* `users`访问。事实上，这是默认行为，为了省略后代表中的行，`SELECT`必须指定`FROM ONLY users`。

```
CREATE TABLE "users" (
  id uuid,
  username text,
  "firstName" text,
  "lastName" text,
  email text,
  organization_id uuid
);

CREATE FOREIGN TABLE "myrealm" (
) INHERITS "users" SERVER "myrealm_server";

SELECT * FROM users; 
```

Enter fullscreen mode Exit fullscreen mode

`SELECT`现在组合了来自每个活动领域的信息，因此对于更高级别的 API，唯一的问题是确保请求用户被授权查看检索到的信息。这正是我们对本地用户表的处理方式，所以我们已经有了授权基础设施，对应用程序其余部分的总体影响是最小的。

## 物化视图

Keycloak 服务器独立于应用程序数据库服务器，这意味着任何涉及用户记录的查询都需要更长的往返时间。毕竟，将数据存储在本地有一些好处！然而，真正的问题不在于拥有数据，而在于确保数据保持最新:我们需要的是一个缓存。物化视图正是如此。

Postgres、SQL Server 和 Oracle 中都有物化视图。如果你使用 MySQL，那你就没那么幸运了(但是，如果你一直在关注这件事，无论如何，这都是 Postgres 或 bust)。它的定义就像一个普通的视图，在`CREATE`和`VIEW`之间的`MATERIALIZED`是一个重要的区别，表明视图查询的结果将被存储直到被刷新。存储的结果也可以像表一样被索引。

```
CREATE MATERIALIZED VIEW cached_users AS
SELECT * FROM users; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们添加一个新领域及其外部表，或者如果现有领域中的信息发生变化(例如，如果我们看到一个以前未知的用户试图登录)，我们可以`REFRESH MATERIALIZED VIEW CONCURRENTLY cached_users;`来....刷新缓存。`CONCURRENTLY`意味着它发生在后台，所以`SELECT`是在数据被检索时发生的，参见旧版本。它没有尽可能地接近实时；如果我们真的想这样做，我们可以用 cron 或 systemd timer，但是对于我们的目的来说，刷新正在创建的新组织或未知用户的身份验证就足够了。

## 总结

总的来说，这增加了数据库设置的复杂性。我们不再运行 stock Postgres，因为必须安装 Multicorn 和 foreign-keycloak-wrapper。不同操作系统上与 Postgres 捆绑在一起的 Python 版本之间的差异也导致了一些问题——通过仔细检查 Postgres 配置和安装日志，这些问题普遍得到了解决，但是很烦人。Docker 解决了一些问题，因为我们可以提供一切就绪的映像，并使用卷来保存数据。

最后，在我们对它们做任何事情之前，领域必须在 Keycloak 中被创建，所以有更多的移动部分需要跟踪。哦好吧；我们有统一的用户缓存，所以应用程序逻辑保持简单，这就是我们想要的。SSO 应该让用户的生活更轻松，而不一定是架构师！