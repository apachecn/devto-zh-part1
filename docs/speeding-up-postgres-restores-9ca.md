# 加速 Postgres 恢复

> 原文：<https://dev.to/adammckerlie/speeding-up-postgres-restores-9ca>

<figure>[![](img/93fde6f7c7f746569afd1e7bc44d78e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i2xAKkHN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AgvIC2B_kDnWb4honBErXeA.jpeg) 

<figcaption>我在整个过程中的感受</figcaption>

</figure>

*原贴于[procrastinatingdev.com](https://procrastinatingdev.com/speeding-up-postgres-restores/)T3】*

最近，我坐下来加速我们开发环境中的数据库恢复过程。像大多数项目一样，我们的数据库开始时很小，几年来增长显著。当我们开始时，数据库只有几 MB 未压缩。现在差不多 2GB 压缩(50GB 未压缩)。我们平均每周恢复一次开发环境，旧的恢复方式已经不再奏效。当我看到“ [DB 还原 foos？](https://xkcd.com/303/)“在我们的松弛渠道中，我知道是时候解决这个问题了。

下面是我加速数据库恢复的过程。

#### 天真的做法

下面基本上是我们的第一个版本的备份和恢复。在我们的主 Postgres 数据库上，我们将运行一个简单的 pg_dump，并将输出通过管道传输到 gzip。当我们想要恢复我们的开发环境时，我们会对压缩文件进行 SCP，解压缩它，然后通过 psql 命令加载它。

```
$ pg_dump db | gzip > dump.gz

real 7m9.882s
user 5m7.383s
sys 2m56.495s

$ gunzip dump.gz

real 2m27.700s
user 1m28.146s
sys 0m41.451s

$ psql db < dump

real 30m4.237s
user 0m21.545s
sys 0m44.331s 
```

*初始进场总时间:39 分 41 秒(32.5 分钟恢复开发)*

这在很长一段时间内都很有效。它很简单，很容易设置，而且当我们的数据库只有几百 MB 时，它也很快。显然，32.5 分钟恢复您的开发数据库是不可接受的。

#### 管道解压缩

我最初的想法是通过使用 [zcat](http://linux.die.net/man/1/zcat) 将压缩文件直接传输到 psql 命令中，以加快我们的恢复时间。你可以认为 zcat 和 [cat](http://linux.die.net/man/1/cat) 命令是一样的，只是对于压缩文件。它解压缩文件并将其打印到标准输出中，然后您可以通过管道将其输入到 psql 命令中。

```
$ pg_dump db | gzip > dump.gz

real 7m9.882s
user 5m7.383s
sys 2m56.495s

$ zcat dump.gz | psql db

real 26m22.356s
user 1m28.850s
sys 1m47.443s 
```

*总时间:33 分 31 秒(26.3 分钟恢复开发，快 20%)*

太好了，这使整个过程加快了 16%,实际恢复加快了 20%。因为 I/O 是一个瓶颈，不写入磁盘为我们节省了 6 分钟。尽管总的来说我还是不开心。浪费 26 分钟恢复我们的开发数据库是不够的，我必须更进一步。

#### 自定义格式

当我深入研究 [pg_dump 文档](http://www.postgresql.org/docs/9.5/static/app-pgdump.html)时，我注意到默认情况下，pg_dump 导出一个纯文本 SQL 文件。正如你在上面看到的，我们压缩了它，使它变得更小以便存储。Postgres 提供了一种定制格式，默认情况下，使用 zlib 来压缩它。我最初的想法是，如果 Postgres 已经以纯文本格式将文件写入磁盘，那么让 Postgres 同时压缩它会更快，而不是必须通过管道将其传输到 gzip。

由于这种自定义格式，我不得不切换到使用 [pg_restore](http://www.postgresql.org/docs/current/static/app-pgrestore.html) ，因为你不能使用 psql 重定向压缩文件。

```
$ pg_dump -Fc db > dumpfc.gz

real 6m28.497s
user 5m2.275s
sys 1m16.637s

$ pg_restore -d db dumpfc.gz

real 26m26.511s
user 0m56.824s
sys 0m15.037s 
```

总*时间 32 分 54 秒(恢复开发 26.4 分钟)。*

我认为实际的备份过程会更快，因为我们不必将输出通过管道传输到 gzip，这是正确的。不幸的是，在您的本地机器上恢复这种自定义格式并没有使这个过程变得更快。回到绘图板。

#### 指定工作

当我陷入一个问题时，我总是做的第一件事就是阅读文档和源代码。Postgres 有很棒的文档，有清晰的布局和标签选项。其中一个选项是，当 pg_restore 执行最耗时的部分、加载数据、创建索引或创建约束时，能够指定并发运行的作业数量。

pg_restore 文档指出，从并发作业的数量开始，一个很好的方法是使用您机器上的内核数量。我的开发环境虚拟机有 4 个内核，但我想看看运行时的不同值。

```
$ pg_dump -Fc db > dumpfc.gz

real 6m28.497s
user 5m2.275s
sys 1m16.637s

$ pg_restore -d db -j 2 dumpfc

real 25m39.796s
user 1m30.366s
sys 1m7.032s 
```

*总时间 32 分 7 秒(恢复 dev 需要 25.6 分钟，比普通 pg_restore 快 3%)。*

好的，这是一个微小的进步，我们能再推一下吗？

```
$ pg_dump -Fc db > dumpfc.gz

real 6m28.497s
user 5m2.275s
sys 1m16.637s

$ pg_restore -d db -j 4 dumpfc.gz

real 22m6.124s
user 0m58.852s
sys 0m34.682s 
```

*总时间 28 分 34 秒(22.1 分钟恢复开发比两个作业快 14%)。*

非常好，在两个作业中指定四个作业可将总体恢复时间加快 14%。在这一点上，我们已经将其从开发环境中的 32.5 分钟加速到 22.1 分钟，增加了 32%！

我现在想的是我能把这个数字推多远？

```
$ pg_dump -Fc db > dumpfc.gz

real 6m28.497s
user 5m2.275s
sys 1m16.637s

$ pg_restore -d db -j 8 dumpfc.gz

real 16m49.539s
user 1m1.344s
sys 0m39.522s 
```

*总时间 23 分 17 秒(恢复设备需要 16.8 分钟，比四个任务快 24%)。*

因此，指定两倍数量的作业作为核心将时间从 22.1 分钟减少到 16.8 分钟。此时，我已经将我们的整个恢复时间加快了 49%,这真是太棒了。

我能更进一步吗？

```
$ pg_dump -Fc db > dumpfc.gz

real 6m28.497s
user 5m2.275s
sys 1m16.637s

$ pg_restore -d db -j 12 dumpfc.gz

real 16m7.071s
user 0m55.323s
sys 0m36.502s 
```

*总时间 22 分 35 秒(16.1 分钟恢复开发，比八个作业快 4%)。*

指定 12 个并发作业确实稍微加快了进程，但总体上影响了虚拟机的 CPU 使用率，使其在恢复时不会过度可用。在这一点上，我决定使用八个任务或双倍数量的内核作为最佳数量。

#### 最后的想法

最后，我们的活动减少了将近一半，从 30 分钟减少到 16 分钟。**这为我们每年节省了超过 72 小时的恢复时间** (6 个设备，每年 52 次恢复，14 分钟)。总的来说，我对这些变化非常满意。将来，我将只考虑恢复数据，而不是整个数据库，看看这样能快多少。

*在* [*加速 Postgres 恢复第二部*](https://procrastinatingdev.com/speeding-up-postgres-restores-part-2/) *中我们进一步提高了时间，将它降低到 5 分钟。*

* * *

*原贴于[procrastinatingdev.com](https://procrastinatingdev.com/speeding-up-postgres-restores/)T3】*