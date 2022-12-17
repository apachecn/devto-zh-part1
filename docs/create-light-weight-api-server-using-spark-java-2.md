# 使用 Spark Java 创建轻量级 api 服务器[2]

> 原文：<https://dev.to/silentsudo/create-light-weight-api-server-using-spark-java-2>

大家好，

继续上一节[https://dev . to/silentsudo/create-light-weight-API-server-using-spark-Java](https://dev.to/silentsudo/create-light-weight-api-server-using-spark-java)

现在我们已经准备好了 hello world 服务器，让我们删除 hello world 并使它成为一个合适的 api 服务器。

我们将按照以下结构安排我们的代码库:

包
- config(服务器上的任何配置)
- models(通常是 POJO 类)
- routes(管理请求的 api 的控制器)
- services(处理来自 routes 的数据的业务逻辑)
- database(暂时我们将使用 java 的集合框架创建内存 db)

首先，我们将做一些服务器配置:
默认情况下，Spark 服务器在 4567 端口上启动，让我们使用
T0 来更改它

我们现在有配置包，我们将使用它。
下面是配置类的样子:

```
import spark.Spark;

import java.net.Inet4Address;
import java.net.UnknownHostException;

public final class Config {

    private  final int HTTP_PORT= 9090;

    public Config() {
        Spark.port(HTTP_PORT);
    }

    public int getPort() {
        return this.HTTP_PORT;
    }

    public String getHost() {
        try {
            return Inet4Address.getLocalHost().getHostAddress();
        } catch (UnknownHostException e) {
            return "http://localhost";
        }
    }

} 
```

下面是我们的主课

```
import in.silentsudo.blabber.config.Config;
import spark.Spark;

/**
 * Greeting world!
 */
public class Main {
    public static void main(String[] args) {
        System.out.println("Starting server...");
        Config config = new Config();

        Spark.get("/sayhello", (request, response) -> {
            return "Hello buddy!!!";
        });

        System.out.println("Open : " + config.getHost() + ":" + config.getPort());
        System.out.println("Server started successfully.");
    }
} 
```

您应该在控制台中看到类似这样的内容，并且您的服务器已经启动并运行，详细信息如下:

```
Starting server...
Open : 172.16.1.211:9090
Server started successfully. 
```

我们还没有做任何令人信服的事情，现在我们使用集合框架创建我们的内存数据库。

设计后端实体:

我们应用程序中的轻量级实体将是:

*   用户(应用程序用户及其信息)
*   Tweet(由拥有 140 个字符的用户创建，可能有也可能没有喜欢)
*   喜欢(只属于一条推文和一个用户，如果没有任何这样的信息，喜欢是无用的，想象一下我们不知道谁是那个人，哪个是推文的喜欢)
*   记录器(记录服务器上发生的所有事情的小类)

设计 URL 结构和方法:

有很多非常好的 rest-api 设计文档:

我从这里取了具体的部分:[https://www . red hat . com/archives/rest-practices/2011-August/pdfa 1 nfejpmmt . pdf](https://www.redhat.com/archives/rest-practices/2011-August/pdfa1nfEjPMmT.pdf)

API 方法:

| 方法 | 范围 | 语义学 |
| --- | --- | --- |
| 得到 | 收藏品 | 检索集合中的所有资源 |
| 得到 | 资源 | 检索单个资源 |
| 头 | 收藏品 | 返回集合中的所有资源(仅标题) |
| 邮政 | 收藏品 | 在集合中创建新资源 |
| 放 | 资源 | 更新资源 |
| 删除 | 资源 | 删除资源 |

URL 结构:

URL 结构在 api 设计中也起着非常关键的作用。我们存储与我们的目的相关的数据，并忽略其他不相关的信息，此后，如果多个客户端(手机、其他 web 服务器、使用 js 的 html 页面)想要访问这些数据，我们使用 rest 接口公开它们，因此我们公开这些数据的方式也应该遵循一些标准模式。

我们将以下列方式定义 url 结构:

*   `ip/pathtoresource/`
*   `ip/pathtoresource/if-subresource`
*   `ip/pathtoresource/if-subresource/if-sub-sub-resource`

考虑到上述模式，让我们看看我们的 apis urls 应该是什么样子的:

*   `ip/users/`(所有用户，通常这是屏蔽谁要告诉全世界我有这么多人)
*   `ip/users/:id`(特定用户，主要在我们想要获取自己的信息时使用，应该限制对当前用户以外的用户数据的调用)
*   `ip/users/:id/tweets`(获取特定用户的所有推文)
*   `ip/tweets/`(所有公开推文)
*   `ip/tweets/:id`(带 id 的特定推文)
*   `ip/likes/:id`(具体喜欢)
*   `ip/users/:id/likes`(获得该用户的所有赞)

如果我们的网址有可疑之处，我会重新考虑...

在下一篇文章中，我们将看到如何使用 Spark-Java 编写 URL，并将特定的资源与它相关联。

感谢您的阅读！