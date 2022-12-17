# 在 Ubuntu 17.04 上将 PostgreSQL 从 9.5 升级到 9.6

> 原文：<https://dev.to/pauloxnet/upgrading-postgresql-from-95-to-96-on-ubuntu-1704>

> 升级到 Ubuntu 17.04 后如何将 PostgreSQL 从 9.5 升级到 9.6

# TL；速度三角形定位法(dead reckoning)

升级到 Ubuntu 17.04 后:

```
sudo pg_dropcluster 9.6 main --stop
sudo pg_upgradecluster 9.5 main
sudo pg_dropcluster 9.5 main 
```

Enter fullscreen mode Exit fullscreen mode

# 升级 PostgreSQL

在 Ubuntu 升级到 17.04 的过程中，你会收到这条消息“配置 PostgreSQL-common”:

```
Obsolete major version 9.5

The PostgreSQL version 9.5 is obsolete, but the server or client packages are still installed.
Please install the latest packages (postgresql-9.6 and postgresql-client-9.6) and
upgrade the existing clusters with pg_upgradecluster (see manpage).

Please be aware that the installation of postgresql-9.6 will automatically create a default cluster 9.6/main.
If you want to upgrade the 9.5/main cluster, you need to remove the already existing 9.6 cluster
(pg_dropcluster --stop 9.6 main, see manpage for details).

The old server and client packages are no longer supported.
After the existing clusters are upgraded, the postgresql-9.5 and postgresql-client-9.5 packages should be removed.

Please see /usr/share/doc/postgresql-common/README.Debian.gz for details. 
```

Enter fullscreen mode Exit fullscreen mode

使用`dpkg -l | grep postgresql`检查安装了哪些版本的 postgres:

```
ii  postgresql                 9.6+179                all     object-relational SQL database (supported version)
ii  postgresql-9.5             9.5.6-0ubuntu0.16.10   amd64   object-relational SQL database, version 9.5 server
ii  postgresql-9.6             9.6.2-1                amd64   object-relational SQL database, version 9.6 server
ii  postgresql-client          9.6+179                all     front-end programs for PostgreSQL (supported version)
ii  postgresql-client-9.5      9.5.6-0ubuntu0.16.10   amd64   front-end programs for PostgreSQL 9.5
ii  postgresql-client-9.6      9.6.2-1                amd64   front-end programs for PostgreSQL 9.6
ii  postgresql-client-common   179                    all     manager for multiple PostgreSQL client versions
ii  postgresql-common          179                    all     PostgreSQL database-cluster manager
ii  postgresql-contrib-9.5     9.5.6-0ubuntu0.16.10   amd64   additional facilities for PostgreSQL
ii  postgresql-contrib-9.6     9.6.2-1                amd64   additional facilities for PostgreSQL 
```

Enter fullscreen mode Exit fullscreen mode

运行`pg_lsclusters`，你的 9.5 和 9.6 主集群应该是“在线”的。

```
Ver Cluster Port Status Owner    Data directory               Log file
9.5 main    5433 online postgres /var/lib/postgresql/9.5/main /var/log/postgresql/postgresql-9.5-main.log
9.6 main    5432 online postgres /var/lib/postgresql/9.6/main /var/log/postgresql/postgresql-9.6-main.log 
```

Enter fullscreen mode Exit fullscreen mode

9.6 已经有一个集群“main”(因为这是默认情况下在软件包安装时创建的)。
这样做是为了让全新的安装开箱即用，而不需要先创建集群，但是当 9.6/main 也存在时，当您尝试升级 9.5/main 时，当然会发生冲突。
推荐的程序是用`pg_dropcluster`删除 9.6 集群，然后用`pg_upgradecluster`升级。

停止 9.6 集群并将其删除。

```
sudo pg_dropcluster 9.6 main --stop 
```

Enter fullscreen mode Exit fullscreen mode

将 9.5 集群升级到最新版本。

```
sudo pg_upgradecluster 9.5 main 
```

Enter fullscreen mode Exit fullscreen mode

您的 9.5 集群现在应该“关闭”，您可以验证运行`pg_lsclusters`

```
Ver Cluster Port Status Owner    Data directory               Log file
9.5 main    5433 down   postgres /var/lib/postgresql/9.5/main /var/log/postgresql/postgresql-9.5-main.log
9.6 main    5432 online postgres /var/lib/postgresql/9.6/main /var/log/postgresql/postgresql-9.6-main.log 
```

Enter fullscreen mode Exit fullscreen mode

检查升级后的集群是否正常工作，然后删除 9.5 集群。

```
sudo pg_dropcluster 9.5 main 
```

Enter fullscreen mode Exit fullscreen mode

完成所有数据检查后，您可以删除旧包。

```
sudo apt-get purge postgresql-9.5 postgresql-client-9.5 postgresql-contrib-9.5 
```

Enter fullscreen mode Exit fullscreen mode

# 免责声明。

在适用法律允许的范围内，本程序不提供任何担保。除非另有书面声明，否则版权所有者和/或其他方“按原样”提供程序，不提供任何形式的明示或暗示担保，包括但不限于对适销性和特定用途适用性的暗示担保。程序质量和性能的全部风险由您承担。如果程序被证明有缺陷，您将承担所有必要的服务、维修或修正的费用。

# 引用

最初发表于[gist.github.com/pauloxnet](https://gist.github.com/pauloxnet)和[www.paulox.net](http://www.paulox.net/2017/04/14/upgrading-postgresql-from-9-5-to-9-6-on-ubuntu-17-04/)