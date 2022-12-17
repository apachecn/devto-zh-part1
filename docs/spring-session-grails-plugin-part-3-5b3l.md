# 春季会议 Grails 插件(第 3 部分)

> 原文：<https://dev.to/jeetmp3/spring-session-grails-plugin-part-3-5b3l>

[![Fork me on GitHub](img/8407139a163848650cee3c86b3c9e5ab.png)T2】](https://github.com/jeetmp3/spring-session)

嗨，伙计们，希望你们一切都好！

自从我的上一篇博文以来，我一直很忙。所以这篇文章是春季 Grails 插件 博客系列的一部分，它将涵盖 JDBC 作为你的数据存储。

在这篇博文中，我将解释如何使用 JDBC 作为你的会话存储。要使用 JDBC 作为数据存储，您需要在数据库中创建两个表。Spring Session 不使用任何类型的 ORM 工具。您需要创建两个表，一个用于会话，另一个用于会话属性，格式将类似于 **<会话表>** 和 **<会话表>_ 属性**。

```
CREATE TABLE SPRING_SESSION (
  SESSION_ID CHAR(36),
  CREATION_TIME BIGINT NOT NULL,
  LAST_ACCESS_TIME BIGINT NOT NULL,
  MAX_INACTIVE_INTERVAL INT NOT NULL,
  PRINCIPAL_NAME VARCHAR(100),
  CONSTRAINT SPRING_SESSION_PK PRIMARY KEY (SESSION_ID)
);

CREATE INDEX SPRING_SESSION_IX1 ON SPRING_SESSION (LAST_ACCESS_TIME);

CREATE TABLE SPRING_SESSION_ATTRIBUTES (
 SESSION_ID CHAR(36),
 ATTRIBUTE_NAME VARCHAR(200),
 ATTRIBUTE_BYTES BYTEA,
 CONSTRAINT SPRING_SESSION_ATTRIBUTES_PK PRIMARY KEY (SESSION_ID, ATTRIBUTE_NAME),
 CONSTRAINT SPRING_SESSION_ATTRIBUTES_FK FOREIGN KEY (SESSION_ID) REFERENCES SPRING_SESSION(SESSION_ID) ON DELETE CASCADE
);

CREATE INDEX SPRING_SESSION_ATTRIBUTES_IX1 ON SPRING_SESSION_ATTRIBUTES (SESSION_ID); 
```

要更改数据存储，您需要在您的`Config.groovy`中添加一个属性。

```
springsession.sessionStore=SessionStore.JDBC 
```

这将把 JDBC 设置为您的数据存储。默认情况下，它会尝试连接 **H2 数据库**。让我们看看一些配置属性及其默认值。

***注:*** *的一些共同性质在第一部分中解释过。对于 JDBC 数据存储库*，情况也是如此。

```
springsession.jdbc.driverClassName="org.h2.Driver" // Driver class default is H2 driver
springsession.jdbc.url="jdbc:h2:~/test" // JDBC Connection string
springsession.jdbc.username="" // JDBC username. Default is "".
springsession.jdbc.password="" // JDBC password. Default is "".
springsession.jdbc.tableName="SessionData" // table name to store sessions

springsession.jdbc.pool.maxActive=10 // Connection pool max active
springsession.jdbc.pool.maxTotal=20 // Connection pool max total
springsession.jdbc.pool.minIdle=3 // Connection pool min idle
springsession.jdbc.pool.maxWaitMillis=10000 // Connection wait time
springsession.jdbc.pool.defaultAutoCommit=true // autocommit true by default
springsession.jdbc.pool.defaultReadOnly=false // read only sessions
springsession.jdbc.pool.defaultTransactionIsolation=Connection.TRANSACTION_READ_COMMITTED // transaction isolation 
springsession.jdbc.pool.validationQuery="SELECT 1" // Validate connection query 
```

默认情况下，它使用 Java 序列化。要使用 JSON 连载请访问本系列的 [**第一博客**](https://dev.to/blogs/2016/12/15/Spring-Session-Grails-Plugin) 。前两步是一样的，但是在第三步<sup>第三步</sup>第**步*用 spring-session 插件*** 注册我的模块类，你必须使用 JDBC 特有的`jackson.modules`属性。

```
springsession.jdbc.jackson.modules = ['demo.SimpleModule'] 
```