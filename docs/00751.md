# 数据库超时

> 原文：<https://dev.to/brightdevs/database-timeouts-4l4p>

上次我已经概述了[超时](https://dev.to%20post_url%202017-10-23-the-importance-of-timeouts%20)的重要性。如果没有经过仔细考虑的超时，我们的应用程序很容易变得没有响应。在这篇文章中，我将重点介绍配置与数据库交互相关的各种超时。我将特别关注关系数据库。然而，这些原理和实践同样适用于其他类型的数据库。

[![Database](img/740dd71f0a602e9adf53aabefc23b77a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--vKVlPL3B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k64c2la5ocsrsfp0yppa.jpg)

## 不同种类的超时

向数据库请求查询结果是后端应用程序最常见的活动之一。让我们将这个简单的任务分解成几个步骤:

1.  [在池](#init)中建立数据库连接
2.  [将连接从池中取出](#pool)
3.  [验证获取的连接](#validate)
4.  [将语句发送到数据库](#send)
5.  [读取查询结果](#read)

上述每个步骤都可能涉及特定的超时配置。细节取决于特定的技术堆栈和我们查询的数据库类型。

### 

处理原始数据库连接几乎总是在连接池的帮助下完成的。与运行一个简单的语句相比，建立到数据库的连接是非常昂贵的。该池通过根据需要重用连接来降低成本。

第一个超时是建立数据库连接之前的最大持续时间。在 JDBC，这可以通过以下方式控制:

*   `connectTimeout`在 [MySQL JDBC 驱动](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-configuration-properties.html)

> 套接字连接超时(毫秒)，0 表示无超时。仅适用于 JDK 1.4 或更高版本。默认为“0”。

默认为**无限‼️**

*   `socketTimeout`在 [MySQL JDBC 驱动](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-configuration-properties.html)

> 网络套接字操作超时(毫秒)(0，默认值表示无超时)。

*   `loginTimeout`在 [MySQL JDBC 驱动](https://docs.oracle.com/javase/8/docs/api/java/sql/DriverManager.html#setLoginTimeout-int-)

> 设置识别驱动程序后，驱动程序在尝试连接到数据库时等待的最长时间(秒)。

默认值 0 表示**无限‼️**

*   `loginTimeout`中的 [PostgreSQL JDBC 驱动](https://jdbc.postgresql.org/documentation/head/connect.html):

> 指定等待建立数据库连接的时间。超时以秒为单位。

**默认为无限‼️**

*   `connectTimeout`中的 [PostgreSQL JDBC 驱动](https://jdbc.postgresql.org/documentation/head/connect.html):

> 用于套接字连接操作的超时值。如果连接到服务器的时间超过该值，连接将被中断。超时以秒为单位，零值表示禁用超时。

*   `socketTimeout`中的 [PostgreSQL JDBC 驱动](https://jdbc.postgresql.org/documentation/head/connect.html):

> 用于套接字读取操作的超时值。如果从服务器读取数据的时间超过了这个值，连接就会关闭。这可以用作强力全局查询超时和检测网络问题的方法。超时以秒为单位，零值表示禁用超时。

你可能已经注意到了上面一个重复出现的主题:**默认超时要么是无限的，要么在驱动级别被禁用**。*在`socketTimeout`和`connectTimeout`的情况下，在 [Linux](https://linux.die.net/man/7/socket) 和 [Windows](https://msdn.microsoft.com/en-us/library/windows/desktop/ms740476(v=vs.85).aspx) 上仍然会涉及系统级超时。然而，这些只是阻止发送和接收操作，JDBC 驱动程序如何与套接字交互在很大程度上是一个实现细节。*

为了演示上述超时在实践中是如何工作的，我们将使用下面的测试用例:

```
class JdbcTimeoutTest {
    @Test
    fun `mysql getConnection`() {
        val mysqlDataSource = mysqlDataSource()

        assertTimeoutPreemptively(Duration.ofMinutes(3)) {
            useResource { mysqlDataSource.connection }
        }
    }

    @Test
    fun `postgresql getConnection`() {
        val mysqlDataSource = postgreSQLDataSource()

        assertTimeoutPreemptively(Duration.ofMinutes(3)) {
            useResource { mysqlDataSource.connection }
        }
    }

    fun useResource(resourceProvider: () -> AutoCloseable) {
        val start = Instant.now()
        try {
            resourceProvider().use {
                println("Completed in ${Duration.between(start, Instant.now())}")
            }
        } catch (e: Exception) {
            println("Error $e after ${Duration.between(start, Instant.now())}")
        }
    }

    fun mysqlDataSource(): MysqlDataSource {
        return MysqlDataSource().apply {
            this.setURL("jdbc:mysql://localhost:3306/database")
            this.user = "user"
            this.setPassword("password")
        }
    }

    fun postgreSQLDataSource(): PGSimpleDataSource {
        return PGSimpleDataSource().apply {
            this.user = "user"
            this.password = "password"
            this.databaseName = "database"
            this.serverName = "localhost"
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了模拟一个行为不当的数据库服务器，我们将使用`netcat`监听标准 MySQL 和 PostgreSQL 端口，例如:

```
nc -k -l 3306 # listen on MySQL port, PostgreSQL uses 5432 by default 
```

Enter fullscreen mode Exit fullscreen mode

以上两个测试都会因为`assertTimeoutPreemptively`而失败。

用于建立连接的最合适的候选者是`loginTimeout`。这在 PostgreSQL 中有效，但在 MySQL 中无效。显然，5.1、6.0 和 8.0 版本中的 MySQL JDBC 驱动程序将该方法实现为 [Noop](https://en.wikipedia.org/wiki/Noop) 。有趣的是，当通过静态方法`java.sql.DriverManager.setLoginTimeout`全局设置**时，可以强制 MySQL 驱动遵守超时。**

 **一个稍微不太正确的选择是使用`connectTimeout`或`socketTimeout`。套接字级别选项的工作与数据库协议无关，因此不可能为整个建立连接操作精确地设置超时。此外，`socketTimeout`不仅适用于初始连接握手，还适用于套接字读取操作。假的`netcat`服务器不适合测试`connectTimeout`，但是我们可以用它来测试`socketTimeout`。PostgreSQL 驱动程序在设置`pgSimpleDataSource.socketTimeout = 2`约 2 秒后正确报告错误。不幸的是，MySQL 驱动程序中的设置`socketTimeout`对`getConnection`行为**没有影响**。有趣的是，无论我将`socketTimeout`设置为什么值，错误都会在大约 26 秒后抛出。我不知道它为什么会这样🤔。

注意 MySQL JDBC 驱动程序的缺点。

### 

重用数据库连接极大地提高了应用程序的性能。然而，编写一个高效且无 bug 的数据库连接池并不是一件容易的事情，因此我们都应该依赖于成熟的解决方案。在 JVM 世界中，当谈到 JDBC 时有多种选择:

*   光号称是最快的，配置旋钮和默认设置数量有限。目前为止我最喜欢的。
*   [DBCP 2](https://commons.apache.org/proper/commons-dbcp/) 一个最近复活的项目，它有可能通过它的`commons-pool2`模块适用于所有的资源池。
*   Tomcat JDBC 连接池通常与许多配置选项一起使用。成为二溴氯丙烷的替代品。

当连接池中没有可用的连接时，请求连接的代码需要等待，直到有可用的连接。需要仔细考虑线程在等待连接时被阻塞的时间。需要注意的是，我们需要考虑两种情况。第一种是当池达到其最大大小并且所有连接都已被使用时。这是指获取连接所需的实际工作非常少的时候。第二种情况是，所有当前打开的连接都在使用中，但是池被允许创建新的连接，因为它还没有满。这里我们需要记住，建立数据库连接的时间可能很容易就达到 200 毫秒左右，因此超时时间不应该太短。下面您将了解如何在提到的连接池中配置超时:

*   Hikari: `connectionTimeout`

> 此属性控制客户端(即您)等待池中连接的最大毫秒数。如果超过这个时间而没有连接可用，将抛出 SQLException。可接受的最低连接超时为 250 毫秒。默认值为 30000 (30 秒)

*   DBCP: `maxWaitMillis`

> 池在抛出异常之前等待连接返回的最大毫秒数(当没有可用连接时)，或者为-1 表示无限期等待。

**默认为无限‼️**

*   雄猫:`maxWait`

> (int)池在抛出异常之前等待连接返回的最大毫秒数(当没有可用连接时)。默认值为 30000 (30 秒)

我的经验是将这个超时设置为小于 5 秒。

### 

数据库连接可以打开几个小时甚至几天。然而，因为涉及到网络，所以在很多情况下，在客户端看似打开的套接字实际上可能是断开的连接的一部分。表现良好的连接池应该避免将这样的连接交给应用程序代码。缓解这个问题的一个常见策略是在将连接从池中取出之前对其进行测试。过去，测试是使用简单的 SQL 查询执行的，例如`SELECT 1`。如今在 JDBC `Connection`上有一种 [`isValid`](https://docs.oracle.com/javase/7/docs/api/java/sql/Connection.html#isValid(int)) 的方法，这种方法将责任转移到驾驶员本身:

> 如果连接尚未关闭并且仍然有效，则返回 true。驱动程序应该提交一个关于连接的查询，或者使用一些其他的机制，当这个方法被调用时，肯定地验证连接仍然是有效的。

*   Hikari: `validationTimeout` :

> 此属性控制测试连接有效性的最长时间。该值必须小于`connectionTimeout`。可接受的最低验证超时为 250 毫秒。默认值为 5000

*   DBCP: `validationQueryTimeout`:

> 连接验证查询失败前的超时时间(秒)。如果设置为正值，该值将通过用于执行验证查询的`Statement`的`setQueryTimeout`方法传递给驱动程序。

**默认为无限‼️**

*   雄猫:`validationQueryTimeout`:

> (int)连接验证查询失败前的超时秒数。这通过对执行 validationQuery 的语句调用 Java . SQL . statement . setquerytimeout(seconds)来实现。池本身不会使查询超时，仍然由 JDBC 驱动程序来强制执行查询超时。小于或等于零的值将禁用此功能。默认值为-1。

**默认为无限‼️**

### 

我们终于找到了最常用的用法。我们发送到数据库的每个查询都应该在语句级或事务级配置一个超时。说到个人陈述，有 [`setQueryTimeout`](https://docs.oracle.com/javase/7/docs/api/java/sql/Statement.html#setQueryTimeout(int)) 可用:

> 将驱动程序等待语句对象执行的秒数设置为给定的秒数。默认情况下，对于正在运行的语句的完成时间没有限制。如果超出限制，将引发 SQLTimeoutException。JDBC 驱动程序必须对 execute、executeQuery 和 executeUpdate 方法应用此限制。

此外，由驱动程序决定上述超时的确切含义:

> 注意:JDBC 驱动程序实现也可能对 ResultSet 方法应用此限制(有关详细信息，请参考您的驱动程序供应商文档)。
> 
> 注意:在语句批处理的情况下，超时是应用于通过 addBatch 方法添加的单个 SQL 命令还是应用于由 executeBatch 方法调用的整批 SQL 命令是由实现定义的(有关详细信息，请参考您的驱动程序供应商文档)。

完成一个查询所需要的时间是非常依赖于用例的，因此我们不应该期望有一个合理的缺省值。相反，我们需要问自己愿意等待查询完成多长时间。很容易忘记这条规则，因此能够全局设置此超时非常方便:

*   DBCP: `defaultQueryTimeout`

> defaultQueryTimeout null 如果为非 null，此整数属性的值将确定查询超时，该查询超时将用于从池管理的连接创建的语句。null 表示将使用驱动程序默认值。

*   Tomcat: `queryTimeout`可通过 [QueryTimeoutInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html#JDBC_interceptors) 获得

> (int as String)为查询超时设置的秒数。小于或等于零的值将禁用此功能。默认值为 1 秒。

*   光:不可用，但是通过包装数据源很容易添加，例如:

```
class CustomTimeoutsDataSource(val innerDataSource: DataSource, private val queryTimeoutProperties: DataSourceConfiguration.QueryTimeoutProperties) : DataSource by innerDataSource {

    override fun getConnection(username: String?, password: String?) = configureTimeouts(innerDataSource.getConnection(username, password))
    override fun getConnection() = configureTimeouts(innerDataSource.connection)

    private fun configureTimeouts(connection: Connection):Connection = CustomTimeoutsConnection(connection, queryTimeoutProperties)

    private class CustomTimeoutsConnection(val innerConnection: Connection, private val queryTimeoutProperties: DataSourceConfiguration.QueryTimeoutProperties)
        : Connection by innerConnection {

        private fun <T : Statement> configure(prepareStatement: T): T {
            //0 means no timeout
            val desiredTimeout = queryTimeoutProperties.statementQueryTimeoutInSeconds ?: 0
            prepareStatement.queryTimeout = desiredTimeout
            LOG.trace("Configure timeout {} seconds for statement {}", desiredTimeout, prepareStatement)
            return prepareStatement
        }

        override fun prepareStatement(sql: String?, autoGeneratedKeys: Int) = configure(innerConnection.prepareStatement(sql, autoGeneratedKeys))
        override fun prepareStatement(sql: String?, resultSetType: Int, resultSetConcurrency: Int, resultSetHoldability: Int) = configure(innerConnection.prepareStatement(sql, resultSetType, resultSetConcurrency, resultSetHoldability))
        override fun prepareStatement(sql: String?) = configure(innerConnection.prepareStatement(sql))
        override fun prepareStatement(sql: String?, columnNames: Array<out String>?) = configure(innerConnection.prepareStatement(sql, columnNames))
        override fun prepareStatement(sql: String?, resultSetType: Int, resultSetConcurrency: Int) = configure(innerConnection.prepareStatement(sql, resultSetType, resultSetConcurrency))
        override fun prepareStatement(sql: String?, columnIndexes: IntArray?)= configure(innerConnection.prepareStatement(sql, columnIndexes))
        override fun prepareCall(sql: String?) = configure(innerConnection.prepareCall(sql))
        override fun prepareCall(sql: String?, resultSetType: Int, resultSetConcurrency: Int) = configure(innerConnection.prepareCall(sql, resultSetType, resultSetConcurrency))
        override fun prepareCall(sql: String?, resultSetType: Int, resultSetConcurrency: Int, resultSetHoldability: Int) = configure(innerConnection.prepareCall(sql, resultSetType, resultSetConcurrency, resultSetHoldability))
        override fun createStatement() = configure(innerConnection.createStatement())
        override fun createStatement(resultSetType: Int, resultSetConcurrency: Int) = configure(innerConnection.createStatement(resultSetType, resultSetConcurrency))
        override fun createStatement(resultSetType: Int, resultSetConcurrency: Int, resultSetHoldability: Int) = configure(innerConnection.createStatement(resultSetType, resultSetConcurrency, resultSetHoldability))

        override fun toString(): String {
            return "CustomTimeoutsConnection(innerConnection=$innerConnection)"
        }
    }

    companion object {
        private val LOG = LoggerFactory.getLogger(CustomTimeoutsDataSource::class.java)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

JDBC 级别`queryTimeout`是在应用程序代码端强制执行的，也就是说，在超时结束后，会执行一个代码来停止查询的执行。MySQL 和 PostgreSQL 的最新版本都提供了数据库服务器级语句超时功能。

*   MySQL:[`MAX_EXECUTION_TIME`T2】](https://dev.mysql.com/doc/refman/5.7/en/optimizer-hints.html#optimizer-hints-execution-time)

> MAX_EXECUTION_TIME 提示只允许用于 SELECT 语句。它对服务器终止语句之前允许执行的时间长度设置了限制 N(超时值，单位为毫秒):

*   PostgreSQL:[T0](https://www.postgresql.org/docs/9.6/static/runtime-config-client.html)

> 从命令从客户端到达服务器时开始，中止任何耗时超过指定毫秒数的语句。如果 log_min_error_statement 设置为 error 或更低，也将记录超时的语句。零值(默认值)会关闭此功能。
> 不建议在 postgresql.conf 中设置 statement_timeout，因为这会影响所有会话。

如果你使用 Hibernate 这样的 JPA 提供者，你可能会被要求使用 [`javax.persistence.query.timeout`](https://docs.jboss.org/hibernate/entitymanager/3.5/reference/en/html/configuration.html) 。然而，从我使用 Hibernate 的经验来看，当全局配置时，这个超时在某些用例中是强制的，而在其他用例中则完全被忽略。报告了多个与此功能相关的错误:[错误 303360](https://bugs.eclipse.org/bugs/show_bug.cgi?id=303360) 、 [HHH-9929](https://hibernate.atlassian.net/browse/HHH-9929) 、[“persistence . XML 中的查询超时不起作用”](https://forum.hibernate.org/viewtopic.php?p=2466990)，其中一些仍然没有得到解决。

JDBC 中没有可用的事务范围超时。但是，用户仍然可以通过 [`@Transactional.timeout`](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/transaction/annotation/Transactional.html#timeout--) 在应用代码中应用超时。

### 保持所有超时时间短

最好的暂停是短暂的。面对性能或吞吐量问题，增加查询或等待超时往往很有诱惑力。但是，这样做会增加服务器上阻塞的资源量，因此很少是一个好的选择。阻塞服务器上越来越多的资源，例如线程，可能会在某个时候导致整个服务器突然崩溃。我尽可能地缩短超时时间，尤其是在经常调用某个 API 的时候。如果你正在寻找关于这个话题的好读物，我强烈推荐[发布它！迈克尔·t·尼加德。本书涵盖了许多与弹性相关的主题，包括超时，并提供了避免超时增加的策略。](https://pragprog.com/book/mnee/release-it)

最初发布于 [brightinventions.pl](https://brightinventions.pl/blog/)

作者 Piotr Mionskowski，软件工程师@光明发明
[邮箱](//piotr.mionskowski@brightinventions.pl) [Stackoverflow](https://stackoverflow.com/users/155213/miensol) [个人博客](https://miensol.pl/)**