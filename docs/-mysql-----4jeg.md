# MySQL 优化:索引、慢速查询、配置

> 原文：<https://dev.to/phpprofi/-mysql-----4jeg>

[![](img/d0cf67492f84f7a0863dafba05352bc2.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5XvQ3qKH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dab1nmslvvntp.cloudfront.net/wp-content/uploads/2017/10/1509294383superhero-534120_1280.jpg)

MySQL 仍然是世界上最受欢迎的关系数据库，但同时也是最不优化的。许多人会保留默认设置，而不会“挖”得更深。在本文中，我们将结合一些相对较新的发现来探讨一些 MySQL 优化技巧。

## t0t 1 优化配置

每个 MySQL 用户为提高性能必须做的第一件事是配置。然而，这一步大多错过了。在 5.7(当前版本)中，默认设置变得比它的前身好得多，但是仍然可以并且很容易地进行改进。

http://phpprofi.ru/blogs/post/76 的читать