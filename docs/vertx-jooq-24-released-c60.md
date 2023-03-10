# vertx-jooq 2.4 发布

> 原文：<https://dev.to/jklingsporn/vertx-jooq-24-released-c60>

> 注意:不幸的是，在 2.4.0 中，对 rxjava1 的依赖还没有完全移除。RX 用户请结帐版本 2.4.1。

我很高兴地宣布 [vertx-jooq 2.4](https://github.com/jklingsporn/vertx-jooq/releases/tag/v2.4.1) 的发布。这一版本背后的主要驱动力是对 [vertx 3.5](http://vertx.io/blog/vert-x-3-5-0-released/) 的依赖升级，从而将 vertx-jooq-rx 中的 rx 依赖从 rxjava 更改为 rxjava2。此外，生成的 POJO 的 JSON 键名现在与它们的数据库对应物相同。这个变化已经在[这里](https://github.com/jklingsporn/vertx-jooq/issues/23)宣布了。最后但同样重要的是，一个 [bug](https://github.com/jklingsporn/vertx-jooq/issues/24) 在处理`JsonObjectConverter`或`JsonArrayConverter`时被修复。

[![](img/7392035f45d1f4cff1cf765ab04bd1c9.png) ](http://feeds.wordpress.com/1.0/gocomments/jensonjava.wordpress.com/1106/) [ ![](img/d9690fd1cd894306527fb34669f215c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1sEkZMD---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://pixel.wp.com/b.gif%3Fhost%3Djensonjava.wordpress.com%26blog%3D28976806%26post%3D1106%26subd%3Djensonjava%26ref%3D%26feed%3D1)