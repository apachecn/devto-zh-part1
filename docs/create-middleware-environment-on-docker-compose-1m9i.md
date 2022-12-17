# 在 Docker Compose 上创建中间件环境

> 原文：<https://dev.to/deadcheat/create-middleware-environment-on-docker-compose-1m9i>

## 本帖中的事情该怎么做

*   使用复合坞站
*   使用这些中间件创建一个环境
    *   MySQL(用于数据库)
    *   Couchbase(用于数据缓存)

## 复合坞站

是管理多个 docker 容器的命令行工具。这对于创建开发环境很有用，因为

*   使用配置文件“docker-compose.yml ”,您可以轻松地将您的环境配置分发给其他开发人员。

## 如何创建它

1.  当然，你最好安装 docker，否则你再也做不到这一点了。
2.  为你的应用程序取一个合适的名字。
3.  创建“docker-compose.yml”到你的目录，编辑如下

```
version: '3'

services:
  couch:
    container_name: "couch"
    image: deadcheat/couchbase
    deploy:
      replicas: 1
    environment:
      BUCKET_NAME: "default"
    ports:
      - 8091:8091
  db:
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=password
      - MYSQL_DATABASE=sample
      - MYSQL_USER=sample
      - MYSQL_PASSWORD=password
    ports:
      - 3306:3306 
```

然后，在您的终端上运行`docker-compose up -d`。
MySQL 和 Couchbase 环境即将启动。

确认连接，
couch base:open[http://localhost:8091/](http://localhost:8091/)
MySQL:连接到 localhost:3306

待续...