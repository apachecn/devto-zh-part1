# GraphQL 作为数据库查询语言

> 原文：<https://dev.to/mtso/graphql-as-a-database-query-language-50im>

*本文最初发表在 [mtso.io](https://mtso.io) 上，名为 [GraphQL 作为数据库查询语言](https://mtso.io/post/graphql-database-language/)。*

GraphQL 生态系统随着开发工具、客户端框架集成、基于反射的生成器和后端框架而快速发展。到目前为止，最未开发的领域是 GraphQL，它是在数据库服务器本身上解释的原生数据查询语言。从类似于[dgraph](https://dgraph.io)(GraphQL over[RocksDB](https://twitter.com/rocksdb/status/676694093095923712))、[postgrapile](https://github.com/postgraphql/postgraphql)(基于 PostgreSQL 模式生成的 GraphQL 端点)和 [tuql](https://github.com/bradleyboy/tuql) (基于 SQLite 数据库生成的 GraphQL 端点)这样的项目中得到启示，我提议开发一种新的数据存储，它使用 graph QL 作为完全本机的查询语言。拥有理解 GraphQL 查询的数据库的主要优势是表达性元数据查询、优化存储和减少开销。

GraphQL 可以用相关信息丰富系统元数据查询。想象一下使用 GraphQL 查询系统目录、用户信息或运行时统计数据。例如，对正在运行的数据库的查询可以填充每个用户的姓名、权限级别和最近一次查询的时间戳，而不需要增加结果数量的表连接。

同时，驱动执行规划器的 GraphQL 语言层有机会优化数据的存储。将 GraphQL 解释器放在离存储引擎更近的地方，可以让数据库服务器在如何存储数据方面做出比应用程序客户机更好的决策。与基于行的数据库并排放置整行相反，面向列的数据库存储列值的方式类似，面向 GraphQL 的数据库可以将一种类型的值组合在一起。甚至可以根据模式变化中指定的参数从模式中推断出索引。

由于语言层运行在数据库服务器本身上，因此在部署时需要管理的工具会更少。GraphQL 数据库可以用一个进程代替两个进程，避免额外的故障点，而不是一个 GraphQL 应用服务器面对一个数据库服务器。此外，所有 GraphQL 查询都将直接在数据库服务器上调用，从而节省了复杂查询的网络往返时间。

这些特性的概念验证实现可以是添加了 GraphQL 解释器的 PostgreSQL 扩展。这将通过使用现有的存储引擎来节省一些工作。在这种策略被证明是有用的之后，可以开发对存储层的进一步优化。

为了减少管理这样一个数据库的摩擦，系统操作应该以 GraphQL 变体的形式公开。这可以在默认的“系统”GraphQL 模式中表现出来，如其他数据库中的系统目录和表。类型可以取代表或集合定义，突变可以取代系统命令，订阅可以取代触发器。

最终，这样的数据库将通过用自然的 GraphQL 语法表达嵌套关系，使数据查询和操作更加强大。有了 GraphQL 作为数据库端的唯一语言，开发过程中需要进行的上下文切换就更少了。

graph.cool 的 Prisma 是这个领域的一个活跃项目。