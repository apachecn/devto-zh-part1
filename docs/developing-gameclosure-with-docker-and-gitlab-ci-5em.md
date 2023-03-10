# 用 Docker 和 Gitlab CI 开发 GameClosure

> 原文：<https://dev.to/netcell/developing-gameclosure-with-docker-and-gitlab-ci-5em>

使用 Docker 和 Gitlab CI 开发 HTML5 游戏框架 GameClosure 的游戏。

## Docker 进行开发

按照 GameClosure 的指导，我制作了一个 docker 文件，其中安装了`gameclosure/devkit`**，我复制了`manifest.json, package.json`，并运行命令**安装依赖项** :** 

```
COPY --chown=node manifest.json package.json ./
    RUN devkit install
    COPY --chown=node ./package.json ./
    RUN npm install
    CMD ["devkit", "serve"] 
```

Enter fullscreen mode Exit fullscreen mode

**我的项目**只需要 **2 个文件夹:** `src`存放源代码，`resources`存放资产。这些是 GameClosure 需要的文件夹，其他的都是依赖项，安装在 docker 镜像中。使用`docker-compose`，我**将这些文件夹**安装到容器上**露出端口**T3。

```
volumes:
        - ./src:/home/node/game/src
        - ./resources:/home/node/game/resources
    ports:
        - 9200:9200 
```

Enter fullscreen mode Exit fullscreen mode

从主机上，我可以像往常一样在`localhost:9200`通过浏览器访问 devkit 页面。

## 连续部署

为了构建这个项目，我需要另一个`docker-compose`配置来**挂载**一个`build`文件夹并且**运行构建命令**而不是`serve`。

```
volumes:
        - ./build:/home/node/game/build
    command: devkit debug browser-mobile 
```

Enter fullscreen mode Exit fullscreen mode

我不想手工构建游戏，所以我使用 Gitlab CI/CD 服务让它**运行**容器，**构建**游戏，**为我将**部署到 Gitlab 页面。我用`pages`任务创建这个`.gitlab-ci.yml`文件，这个任务是*将游戏构建成* `*public*` *工件*:

> 与只能构建 Jekyll 的 Github Pages 不同，Gitlab Pages 可以通过其 CI/CD 服务自动构建几乎任何东西。

```
pages:
        stage: deploy
        script:
            - docker-compose -f docker-compose.yml -f docker-compose.browser-mobile.yml up
            - cp -a build/debug/browser-mobile/. public/
        artifacts:
            paths:
                - public 
```

Enter fullscreen mode Exit fullscreen mode

现在，每次我推我的 Gitlab 库，游戏将在 Gitlab 页面上构建和服务。**