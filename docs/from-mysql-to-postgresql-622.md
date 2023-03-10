# 从 MySQL 到 PostgreSQL

> 原文：<https://dev.to/djangotricks/from-mysql-to-postgresql-622>

[![](img/7771db9a5b8cd1d0aea7de8f05f4e85f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--5cSjbjaF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://4.bp.blogspot.com/-TOhBXgENJP8/WisGv1C6KaI/AAAAAAAAB0U/UTZPJrY380AY7cVrttfZ5ggkZtiYRS31gCLcBGAs/s1600/From%252BMySQL%252Bto%252BPostgreSQL.png)

在本文中，我将指导您完成将 Django 项目从 MySQL 迁移到 PostgreSQL 的步骤。

MySQL 数据库已被证明是中小型项目的良好开端。这是众所周知的，并广泛使用，并有良好的文件。此外，还有一些易于管理的优秀客户端，如 [phpMyAdmin (web)](https://www.phpmyadmin.net/) 、 [HeidiSQL (windows)](https://www.heidisql.com/) 或 [Sequel Pro (macOS)](https://www.sequelpro.com/) 。然而，在我的职业生涯中，有一些不幸的时刻，来自不同项目的数据库因为大型查询或文件系统错误而崩溃。此外，由于应用程序级别的不同错误，MySQL 数据库多年来一直出现数据库完整性错误。

当一个人考虑扩展一个项目时，他们必须选择更合适的东西。它应该是快速、可靠的，并且很好地支持关系数据库的 ANSI 标准。这是大多数顶级 Django 开发人员使用的东西。大多数专业人士选择的数据库恰好是 PostgreSQL。PostgreSQL 支持使用 MySQL 无法实现的几个特定于供应商的特性，例如多维数组、JSON 字段、键值对字段、不区分大小写的特殊文本字段、范围字段、特殊索引、全文搜索等。对于一个新手来说，我所知道的最好的客户端——[pg admin(MAC OS，linux，windows)](https://www.pgadmin.org/)——与 MySQL 客户端相比，一开始可能看起来太复杂，但是因为你有 Django 管理和方便的 ORM，你可能不需要太频繁地检查原始格式的数据库。

那么从 MySQL 迁移到 PostgreSQL 需要什么呢？我们将通过几个步骤来完成，我们将使用 **pgloader** 来帮助我们进行数据迁移。我从 [Louise Grandjonc](https://www.slideshare.net/LouiseGrandjonc) 那里了解到这个工具，她正在 [DjangoCon Europe 2017](https://djangotricks.blogspot.com/2017/04/recap-of-djangoconeurope-2017.html) 上做关于 PostgreSQL 查询优化的演讲。

迁移的一个先决条件是通过测试。您需要进行功能测试来检查所有页面是否都正常运行，还需要进行单元测试来检查至少最关键或最复杂的类、方法和函数。

## 1。准备您的 MySQL 数据库

确保您的生产 MySQL 数据库迁移状态是最新的:

```
(env)$ python manage.py migrate --settings=settings.production 
```

Enter fullscreen mode Exit fullscreen mode

然后创建生产 MySQL 数据库的本地副本。我们将使用它进行迁移。

## 2。安装 pgloader

正如我之前提到的，对于数据库迁移，我们将使用一个名为 **pgloader** 的工具(版本 3.4.1 或更高)。这个工具是由[迪米特里·方丹](https://twitter.com/tapoueh)编写的，在 GitHub 上作为[开源项目提供。您可以从源代码编译所需的版本。或者如果你用的是 macOS，可以用家酿安装:](https://github.com/dimitri/pgloader/tree/v3.4.1) 

```
$ brew update
$ brew install pgloader 
```

Enter fullscreen mode Exit fullscreen mode

请注意，PostgreSQL 也将作为依赖项安装。

## 3。创建新的 PostgreSQL 用户和数据库

与 MySQL 不同，使用 PostgreSQL 创建新的数据库用户和数据库通常发生在 shell 中，而不是在数据库客户机中。

让我们创建一个同名的用户和数据库`myproject`。

```
$ createuser --createdb --password myproject
$ createdb --username=myproject myproject 
```

Enter fullscreen mode Exit fullscreen mode

`--createdb`参数将启用创建数据库的权限。`--password`参数将提供输入密码。`--username`参数将设置创建的数据库的所有者。

## 4。创建模式

在设置中将项目链接到这个新的 PostgreSQL 数据库，例如:

```
DATABASES = {
    'postgresql': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': get_secret("DATABASE_NAME"),
        'USER': get_secret("DATABASE_USER"),
        'PASSWORD': get_secret("DATABASE_PASSWORD"),
    },
}
DATABASES['default'] = DATABASES['postgresql'] 
```

Enter fullscreen mode Exit fullscreen mode

在这里，定制的`get_secret()`函数从环境变量或文本文件中返回敏感信息，这些信息不受版本控制的跟踪。它的实现取决于你。

运行迁移以在新的 PostgreSQL 数据库中创建表和外键约束:

```
(env)$ python manage.py migrate --settings=settings.local 
```

Enter fullscreen mode Exit fullscreen mode

## 5。创建数据迁移脚本

**pgloader** 使用配置文件和定义如何处理迁移的设置。让我们用以下内容创建配置文件`myproject.load`:

```
LOAD DATABASE
    FROM mysql://mysql_username:mysql_password@localhost/mysql_dbname
    INTO postgresql:///myproject
    WITH truncate, data only, disable triggers, preserve index names, include no drop, reset sequences
    ALTER SCHEMA 'mysql_dbname' RENAME TO 'public'
; 
```

Enter fullscreen mode Exit fullscreen mode

## 6。运行数据迁移

现在该复制数据了:

```
$ pgloader myproject.load 
```

Enter fullscreen mode Exit fullscreen mode

通常你会得到一堆关于类型转换的警告。这些可能可以忽略，因为脚本会在导入时对如何转换数据做出最佳猜测。此外，如果您得到关于重复数据或带有外键的表丢失条目的错误，您将需要修复 MySQL 数据库中的问题，然后重复该过程。在这种情况下，清理 MySQL 数据库，更新您的本地副本，用`dropdb`和`createdb`命令重新创建 PostgreSQL 数据库，运行 Django 迁移来创建数据库模式，并再次复制数据。

## 7。改编代码

当数据库成功迁移后，我们应该运行 Django 项目测试，并修复项目代码中所有 PostgreSQL 特有的问题。运行 Django ORM 的代码运行起来会很顺畅，但是很可能会出现原始 SQL、QuerySet 的`extra()`方法和类型转换的问题。

通常，您可能需要记住以下差异:

*   PostgreSQL 查询中的字符串值总是用“单引号”括起来。

*   PostgreSQL 不像 MySQL 那样在自动比较值时转换类型。如果您使用任何原始 SQL，您将需要在比较之前做一些转换，如`CAST(blog_post.id AS text) = likes_like.object_id`或`blog_post.id::text = likes_like.object_id`。MySQL 不理解后一种双冒号语法，所以如果您想支持这两种数据库，您将需要为每个数据库管理系统编写特定于供应商的案例。

*   PostgreSQL 对于字符串比较是区分大小写的，所以在 QuerySet 过滤器中，您需要使用`* __iexact`查找而不是`*__ exact`和`* __icontains`查找而不是`*__ contains`。

*   排序时，使用`Lower()`函数将列转换为小写:

    ```
    from django.db import models
    posts = Post.objects.order_by(models.Lower('title')) 
    ```

*   使用`* __in`查找时，确保列出的元素类型与模型字段的类型相匹配。例如，您可能有一个具有通用关系的`Like`模型，即`content_type`和`object_id`字段，它们将一个通用外键组合到任何模型实例中。`object_id`字段通常是字符串类型，但是相关模型的 id 可能是整数、字符串或 UUIDs。如果您想获得主键为整数的喜欢的帖子，您需要在将它们分配给过滤器中的`pk__ in`查找之前，将`object_id`值转换为整数:

    ```
    liked_ids = map(int, Like.objects.filter(
        user=request.user,
        content_type=ContentType.objects.get_for_model(Post)
    ).values("object_id", flat=True))

    liked_posts = Post.objects.filter(pk__in=liked_ids) 
    ```

## 8。重复生产过程

当您确定迁移过程顺利并且所有 Django 测试都通过时，您可以关闭您的生产网站，使用最新的生产数据在本地重复迁移过程，将迁移的本地数据库复制到生产服务器，更新生产代码，安装新的依赖项，并使网站重新联机。

要创建数据库转储，可以使用命令:

```
$ pg_dump --format=c --compress=9 --file=myproject.backup myproject 
```

Enter fullscreen mode Exit fullscreen mode

使用命令:
从转储中恢复或创建数据库

```
$ dropdb --username=pgsql myproject
$ createdb --username=myproject myproject
$ pg_restore --dbname=myproject --role=myproject --schema=public myproject.backup 
```

Enter fullscreen mode Exit fullscreen mode

我可能错过了一些要点，有一些方法可以自动化生产升级过程，但是您已经明白了。

## 结论

PostgreSQL 比 MySQL 更具限制性，但它提供了更好的性能、更高的稳定性和更好的标准遵从性。此外，PostgreSQL 中有许多 MySQL 中没有的特性。运气好的话，一天之内就能把项目从 MySQL 切换到 PostgreSQL。

*本帖最初发表于[djangotricks.blogspot.com](http://djangotricks.blogspot.com/2017/12/from-mysql-to-postgresql.html)T3】*