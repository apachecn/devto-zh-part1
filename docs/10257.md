# 从 Grunt 运行 Docker

> 原文：<https://dev.to/grahamcox82/running-docker-from-grunt>

在最近的帖子和反馈之后，我又开始玩 Node 了。这一次，我已经解决了为开发和验证测试运行建立数据库的问题。正如我之前提到的，在我的 Java 世界中，我总是嵌入——或者在某些情况下伪嵌入——数据库，它作为应用程序启动的一部分启动，以便开发运行或验证测试运行尽可能地自包含。在 Node 中，这并不容易实现。

输入 Docker。

更具体地说，输入 [Docker Compose](https://docs.docker.com/compose/) 、 [Grunt](http://gruntjs.com/) 和 [Grunt-Shell](https://github.com/sindresorhus/grunt-shell) 。我设法拼凑了一个解决方案，生成 Docker Compose，然后运行构建的其余部分，最后关闭 Docker 容器。这意味着我可以运行“grunt start”或“grunt verify ”,让所有的东西都启动起来，然后知道它们都被干净地关闭了。

当然，重要的是如何实现。开始了。请注意，我还使用了棒极了的[任务管理器](https://www.npmjs.com/package/task-master)模块来帮助我的简单配置更加简洁。除了你在这里看到的，这对任何事情都没有影响。

首先，我写了一些 Docker 编写文件。我有一个给开发人员，一个给测试人员。在这种情况下，我使用 Neo4J，但您可以使用几乎任何具有 Docker 图像的东西，包括您自己开发的图像，如果您愿意的话。我个人创建了一个`docker`目录，其中有一个`dev.yml`和一个`test.yml`文件。对于这个拼凑的实现来说，这并不重要。这是我的`dev.yml`文件:

```
version: '2'
services:
    neo4jdev:
        image: neo4j:3.1.0
        ports:
            - "7474:7474"
            - "7687:7687"
        environment:
            - NEO4J_AUTH=none 
```

Enter fullscreen mode Exit fullscreen mode

启动它将为我提供一个运行 Neo4J 的环境，我可以访问“ [http://localhost:7474](http://localhost:7474) ”上的 Web 管理 UI 和 [http://localhost:7687](http://localhost:7687) 上的 Bolt API(来自节点)。

接下来实际上是运行它。这是使用 Grunt-Shell 完成的，它的配置是:

```
const execSync = require('child_process').execSync;
const grunt = require('grunt');

function buildDockerCommand(env, cmd) {
    return `docker-compose -p testing${env} -f docker/${env}.yml ${cmd}`;
}

module.exports = {
    'command': (env) => {
        process.on('exit', () => {
            grunt.log.ok('Killing docker');
            execSync(buildDockerCommand(env, 'down'));
            if (env === 'test') {
                grunt.log.ok('Removing docker containers');
                execSync(buildDockerCommand(env, 'rm -f'));
            }
        });
        return buildDockerCommand(env, 'up -d');
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

这实际上是:

*   设置一个进程退出挂钩来停止容器，如果环境是“测试”的就删除它们
*   启动集装箱

我在 Grunt 中的别名是:

```
{  "start":  [  "build",  "shell:docker:dev:up",  "express:dev"  ],  "verify":  [  "build",  "build:verify",  "shell:docker:test:up",  "express:verify",  "mochaTest:verify"  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

就这么简单。“grunt start”命令现在将产生“dev”Docker 环境，然后根据它运行我的应用程序。“grunt verify”命令将产生“test”Docker 环境，对它运行我的应用程序，然后对这个正在运行的应用程序运行验证测试。在两种情况下 Grunt 完成之后——要么通过测试完成，要么通过按 Ctrl-C——Docker 环境会关闭。

做到这一点确实需要时间，但不是很多。(我的验证跑目前是 10 秒左右，从头到尾。不过这还不包括任何 UI 测试。)第一次在新机器上运行它们时，必须下载所有 Docker 映像，但除了一些 CI 设置之外，这应该是一次性的。