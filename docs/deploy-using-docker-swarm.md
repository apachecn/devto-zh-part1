# 使用 docker swarm 部署

> 原文：<https://dev.to/svinci/deploy-using-docker-swarm>

我刚刚完成了这个教程，展示了如何使用 ReactJS 和 Redux 建立一个网站。出于测试目的，我还展示了如何使用 nodeJS 构建 REST API。

现在，让我们使用 docker swarm 部署这两个组件。

## 安装

首先，您需要从这里的[克隆 React 教程库。](https://github.com/svinci/react-example)

现在，你需要安装 Docker。安装说明可在[这里](https://www.docker.com/)找到。

一旦你运行了 docker，你需要重启 docker 守护进程，让它在 swarm 模式下运行。这可以通过运行以下命令来实现:

```
$ docker swarm init 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以走了。

## Docker 图片

现在我们需要为我们的 API 和 UI 编写 docker 图像。

### API 图像

让我们从 API 开始。这是我们的 NodeJS API 的 docker 文件。

```
FROM node:boron

RUN mkdir -p /usr/src/app/config
WORKDIR /usr/src/app

COPY package.json /usr/src/app/
RUN npm install

COPY config/default.json /usr/src/app/config/default.json
COPY index.js /usr/src/app/index.js

EXPOSE 8100

CMD ["npm", "run", "start"] 
```

Enter fullscreen mode Exit fullscreen mode

我们正在从`node:boron`(节点 6)建立我们的形象。然后我们在`/usr/src/app`中创建一个目录来部署 API，复制`package.json`并安装依赖项。

然后，我们复制配置和 index.js 文件。我们暴露端口 8100 并启动服务器。

现在我们构建运行`docker build -t svinci/todo-api .`的映像。请随意更改名称空间。

### UI 图像

现在，用户界面将变得更加复杂。容器必须部署一个 NGINX 作为 UI 的 web 服务器，并且它必须有规则来访问通过它的 API。

让我们从制作一个名为`conf`的目录开始，它将保存 NGINX 配置。在其中，我们将使用以下内容编写一个名为`nginx.conf`的文件。

```
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '[$remote_addr] [$time_local] [$request] '
                      'status code: $status, response size: $body_bytes_sent, referer: "$http_referer", '
                      'user agent: "$http_user_agent", $request_time seconds';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;

    keepalive_timeout  65;

    gzip on;
    gzip_disable "msie6";

    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_min_length 256;
    gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript application/vnd.ms-fontobject application/x-font-ttf font/opentype image/svg+xml image/x-icon;

    include /etc/nginx/conf.d/*.conf;
} 
```

Enter fullscreen mode Exit fullscreen mode

这基本上设置了队列大小、mime 类型、访问日志格式和 gzip。现在，在`conf`的一个名为`conf.d`的子目录中，我们将在一个名为`default.conf`的文件中编写以下内容。

```
server {
    listen       80;
    server_name  localhost;

    location /todos {
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_pass http://api:8100;
    }

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

这会将监听端口设置为 80，配置一个到 API 的代理，并设置站点入口点。

现在，完成了这个，这是 done 文件。

```
FROM nginx

COPY conf /etc/nginx

COPY index.html /usr/share/nginx/html
COPY bundle.js /usr/share/nginx/html

EXPOSE 80 
```

Enter fullscreen mode Exit fullscreen mode

我们从`nginx`开始构建我们的映像，复制我们的 NGINX 配置，并将我们的包和 index.html 复制到 web 服务器的默认静态内容位置。然后我们暴露端口 80。

现在，在实际构建 docker 映像之前，我们必须做一点小小的修改。这个例子本来是要在本地运行的，所以我们需要修改`todo-site/src/client.js`下的文件，只通过`/todos`来改变`http://localhost:8100/todos`，这样 UI 通过我们的 NGINX 来执行请求。应该是这样的:

```
import * as superagent from "superagent";

export function get() {

    return new Promise((resolve, reject) => {
        superagent.get("/todos")
            .end((error, result) => {
                error ? reject(error) : resolve(result.body);
            });
    });

}

export function add(text) {

    return new Promise((resolve, reject) => {
        superagent.post("/todos")
            .send({'text': text})
            .end((error, result) => {
                error ? reject(error) : resolve(result.body);
            });
    });

}

export function toggle(id) {

    return new Promise((resolve, reject) => {
        superagent.patch("/todos/" + id)
            .end((error, result) => {
                error ? reject(error) : resolve(result.body);
            });
    });

} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们构建运行`npm install && npm run build && docker build -t svinci/todo-site .`的映像。

## 作曲

我们现在必须编写我们的合成文件。这个文件定义了我们需要运行的所有服务，包括依赖项、复制因子、网络等等。

我们需要将下面的内容写入到资源库根目录下的一个名为`compose.yml`的文件中。

```
version: "3"

services:
  api:
    image: svinci/todo-api
    networks:
      - todo_network
    ports:
      - 8100
    deploy:
      replicas: 1
      update_config:
        parallelism: 1
      restart_policy:
        condition: on-failure
  site:
    image: svinci/todo-site
    ports:
      - 80:80
    networks:
      - todo_network
    depends_on:
      - api
    deploy:
      replicas: 1
      update_config:
        parallelism: 1
      restart_policy:
        condition: on-failure

networks:
  todo_network: 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里定义了两种服务:

*   API:To Do REST API。
*   站点:我们的 NGINX，包含静态内容。

如您所见，我们正在设置它需要使用的图像，以及它们所属的网络。

我们还定义了站点依赖于 API 来工作。

还提供了部署配置。注意每个服务的`deploy`下面的`replicas`，我们可以在这里有现成的冗余。

## 部署

现在，让我们部署它。要进行部署，我们需要运行以下命令:

```
docker stack deploy --compose-file=compose.yml todo_stack 
```

Enter fullscreen mode Exit fullscreen mode

如果你几秒钟后在你的浏览器中访问`http://localhost/`(NGINX 需要 15 秒启动)，你会看到网站完全运行。

## 结论

整理东西很容易，我真的喜欢这样工作，因为我不需要在我的电脑上安装太多软件(比如 NGINX)。此外，如果您在生产中像这样部署，您的本地环境将会大量重组生产，这很棒。

你可以在`docker-swarm`分支上的这里找到这个例子[的源代码。](https://github.com/svinci/react-example)

评论里见！