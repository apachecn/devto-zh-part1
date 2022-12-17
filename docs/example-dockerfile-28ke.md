# 示例 Dockerfile 文件

> 原文：<https://dev.to/adamkdean/example-dockerfile-28ke>

下面是一个 Dockerfile 文件示例。它安装 nginx 并将/src 添加到 wwwroot。确保文件是 755。

```
# DOCKER-VERSION 0.9.1

FROM ubuntu
MAINTAINER Adam K Dean

RUN apt-get update
RUN apt-get -y install nginx

RUN echo "daemon off;" >> /etc/nginx/nginx.conf
ADD default /etc/nginx/sites-available/default
ADD /src /var/www

EXPOSE 80

CMD ["nginx"] 
```

Enter fullscreen mode Exit fullscreen mode

`FROM`定义基础图像。

`RUN`运行一个命令，例如用它来安装软件。

将本地文件添加到容器中。

`EXPOSE`公开一个端口，以便主机可以映射一个端口，通过隧道连接到该端口。

`CMD`定义容器启动时将运行的命令。只能有一个命令，如果有多个，将使用最后一个。您可以像这样向它传递参数:

```
CMD ["service", "parameter"] 
```

Enter fullscreen mode Exit fullscreen mode