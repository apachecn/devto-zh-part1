# 保护 Docker 容器上的 Node.js + RethinkDB + TLS 设置

> 原文：<https://dev.to/lirantal/securing-a-nodejs--rethinkdb--tls-setup-on-docker-containers>

[![](img/9d8e9c3797e0adb6bb58d77342382943.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xi6EXGF3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AC64ZrPVIob7heHW9At28Dw.jpeg)

# [T1】简介](#intro)

我们在不同的项目中使用 RethinkDB。它不用于任何类型的大数据应用程序，而是作为一个 NoSQL 数据库，通过实时更新和关系表支持来增加内容。

# Node.js 生态系统

RethinkDB 有一个官方支持的 Node.js 驱动程序，以及一个由社区维护的驱动程序 rethinkdbdash，它是基于承诺的，并提供连接池。还有一个名为 rethinkdb-migrate 的数据库迁移工具，它有助于管理数据库更改，如模式更改、数据库播种、拆除和拆除功能。

# 重新设计坞站设置

我们将使用 docker hub 中的官方 RethinkDB docker 映像，并利用 docker-compose.yml 来使它旋转起来(稍后您可以向此设置添加其他服务)。

docker-compose.yml 的一个很好的例子:

```
version: '2'
services:
  rethinkdb:
    image: rethinkdb:latest
    ports:
      - "8181:8080"
      - "48015:28015"
    volumes:
      - ./tls:/tls 
```

Enter fullscreen mode Exit fullscreen mode

# 重新思考数据库 SSL 设置

合成文件将本地 tls 目录作为映射卷挂载到容器中。tls/目录将包含我们的证书文件，合成文件反映了这一点。

# 证书

为了建立一个安全的连接，我们需要使用证书来促进它，所以一个
初始技术步骤:

```
cd tls/
openssl genrsa -out key.pem 2048
openssl req -new -x509 -key key.pem -out cert.pem -days 3650 -subj '/CN=domain.com’
cp cert.pem ca.pem 
```

Enter fullscreen mode Exit fullscreen mode

重要注意事项:

*   规范名称(即 CN 值)被设置为 RethinkDB 驱动程序将连接到的域。这里以 domain.com 设置为例，在你的本地开发环境中大概应该只设置为 localhost。
*   将证书复制到证书颁发机构实际上是从属服务器加入集群所需的额外步骤，因此这不是强制性的。

# 用 SSL 启动 RethinkDB

更新合成文件以包含一个命令配置，该命令配置使用所有必需的 SSL 配置
启动 RethinkDB 进程

```
command: ["rethinkdb", "--tls-min-protocol", "TLSv1", "--tls-ciphers", "EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH:AES256-SHA", "--canonical-address", "domain.com", "--http-tls-key", "/tls/key.pem", "--http-tls-cert", "/tls/cert.pem", "--driver-tls-key", "/tls/key.pem", "--driver-tls-cert", "/tls/cert.pem", "--bind" ,"all"] 
```

Enter fullscreen mode Exit fullscreen mode

重要注意事项:

*   第一个命令参数— tls-min-protocol 和— tls-ciphers 用于处理旧版本的 SSL(适用于 Mac OS 设置)
*   注意— canonical-address 参数也被设置为 domain.com，如果您使用 CN=localhost 创建了自签名证书，您可能希望将其更改为 localhost

您会注意到没有任何与群集相关的配置，但如果需要，您也可以添加它们，以便它们可以加入 SSL 连接:—cluster-TLS—cluster-TLS-key/TLS/key . PEM—cluster-TLS-cert/TLS/cert . PEM—cluster-TLS-ca/TLS/ca . PEM

# Node.js 驱动设置

RethinkDB 驱动程序支持 ssl 可选对象，该对象使用 ca 属性设置证书，或者设置 rejectUnauthorized 属性以在连接时接受或拒绝自签名证书。
要传递给驱动程序的 ssl 配置片段:

```
ssl: {
  rejectUnauthorized: false
  // ca: fs.readFileSync(__dirname + '../tls/cert.pem').toString().trim()
} 
```

Enter fullscreen mode Exit fullscreen mode

# 重新思考 DB 密码设置

既然连接是安全的，那么只有使用非默认的用户名/密码进行连接才有意义。

```
Security Alert! RethinkDB ships with a default user and no password set which is insecure to say the least and was one of the main reasons for hundred of thousands of MongoDBs getting pwned on AWS a while back.

PLEASE CHANGE THE DEFAULT USER ACCOUNT 
```

Enter fullscreen mode Exit fullscreen mode

要设置它，请更新合成文件，使其也包含— initial-password 参数，以便您可以设置默认管理员用户的密码。例如:

```
command: ["rethinkdb", "--initial-password", "changeMe"] 
```

Enter fullscreen mode Exit fullscreen mode

当然，您需要将这个参数附加到上面的 compose 文件中的其余命令行选项中。

```
Preferably, don’t store the password on the Dockerfile but rather use an environment variable or another method that doesn’t expose secrets. 
```

Enter fullscreen mode Exit fullscreen mode

现在，更新 Node.js 驱动程序设置，使用用户名和密码进行连接:

```
{
  user: 'admin',
  password: 'changeMe'
} 
```

Enter fullscreen mode Exit fullscreen mode

恭喜你！您现在有资格“准备生产贴纸”。

别担心，我已经把它们寄到你的地址了。