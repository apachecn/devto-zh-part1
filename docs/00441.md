# 可配置工件:如何像专业人员一样部署

> 原文：<https://dev.to/kmaschta/configurable-artifacts-how-to-deploy-like-a-pro-4fhk>

当项目团队成长时，特性部署变得更加频繁。自动化这些部署对于优化开发工作流变得至关重要。在我看来，最佳实践是基于工件的部署，这是我尽可能使用的救命流程。这很受欢迎，也是十二因素应用程序模式的一部分。

本文通过一个实际的例子，用简单的术语说明了基于工件的部署。

## 什么是部署神器

工件并不是一个新东西，但是像所有好的实践一样，写下来会更好。

部署工件(或`build`)是在生产环境中运行的应用程序代码:编译、构建、捆绑、缩小、优化等等。最常见的是，它是一个二进制文件，或者是压缩在归档中的一组文件。

在这种状态下，您可以存储和版本化一个工件。工件的最终目标是尽可能快地下载到服务器上，并立即运行，没有服务中断。

此外，工件应该是可配置的，以便可以在任何环境中部署。例如，如果您需要部署到临时和生产服务器，您应该能够使用相同的工件。

是的，您没有看错:只有配置必须改变，而不是工件本身。这可能看起来有害或者困难，但是它是部署工件的主要特性。如果您必须为两个环境构建两次您的工件，您就错过了整个要点。

## 示例项目:基本代理

让我们举一个要部署的示例项目。我将编写一个基本的 HTTP 代理，在 Node.js 中为每个请求添加一个随机的 HTTP 头。

**温馨提示:**代码可在[本要诀](https://gist.github.com/Kmaschta/0920c6a7781cdf15c37a51b370e4fb66)中找到。

首先，安装依赖项:

```
$ mkdir deployment-example && cd $_ # cd into the directory just created
$ npm init --yes
$ npm install --save express axios reify 
```

Enter fullscreen mode Exit fullscreen mode

这里是服务器，在添加了一个随机头:
之后，将所有请求代理到`http://perdu.com`后端

```
// server.js
import uuid from 'uuid';
import axios from 'axios';
import express from 'express';

const port = process.env.NODE_PORT || 3000;
const baseURL = process.env.PROXY_HOST || 'http://perdu.com/';

const client = axios.create({ baseURL });
const app = express();

app.get('/', (req, res) => {
    const requestOptions = {
        url: req.path,
        method: req.method.toLowerCase(),
        headers: { 'X-Random': uuid.v4() },
    };

    return client(requestOptions).then(response => res.send(response.data));
});

app.listen(port);
console.log(`Proxy is running on port ${port}. Press Ctrl+C to stop.`); 
```

Enter fullscreen mode Exit fullscreen mode

**提示:**你注意到我是如何通过`process.env`使用环境变量的吗？这是我观点的核心，记住它以后再说。

运行服务器的小 makefile:

```
start:
    # reify is a small lib to fully support import/export without having to install the babel suite
    node --require reify server.js 
```

Enter fullscreen mode Exit fullscreen mode

服务器可以在本地环境下运行，一切都很好，“它在我的机器上工作”:

```
$ make start
> Proxy is running on port 3000\. Press Ctrl+C to stop. 
```

Enter fullscreen mode Exit fullscreen mode

### 打造神器

现在让我们将这段代码发布到一个测试环境中，以便在真实的环境中测试它。

此时，冻结代码的一个版本是个好主意(例如，用 Git 标签或 GitHub 版本)。

准备部署的最简单的方法是用源代码及其所有依赖项**构建**一个 zip 文件。我将把下面的目标添加到`makefile`中，用最近提交的标识符创建一个 zip:

```
build:
    mkdir -p build
    zip 'build/$(shell git log -1 --pretty="%h").zip' Makefile package.json -R . '*.js' -R . '*.json' 
```

Enter fullscreen mode Exit fullscreen mode

而且很管用:

```
$ make build
$ ls build/
> 4dd370f.zip 
```

Enter fullscreen mode Exit fullscreen mode

这里简化了构建步骤，但是在实际项目中，它可能意味着一个 bundler、一个 transpiler、一个 minifier 等等。所有这些冗长的任务都应该在构建阶段完成。

产生的 zip 文件就是我们所谓的**工件**。它可以部署在外部服务器上，或者存储在 S3 存储桶中以备后用。

**提示:**一旦你找到适合你的构建过程，自动化它！通常，像 Travis 或 Jenkins 这样的持续集成/持续交付(CI/CD)系统运行测试，如果测试通过，就构建工件以便存储它。

### 部署神器

要部署工件，只需在服务器上复制这个文件，提取它，并运行代码。我再次将它自动化为一个`makefile`目标:

```
TAG ?=
SERVER ?= proxy-staging

deploy:
    scp build/$(TAG).zip $(SERVER):/data/www/deployment-example/
    ssh $(SERVER) " \ cd /data/www/deployment-example/ \ unzip $(TAG).zip -d $(TAG)/ && rm $(TAG).zip \      # unzip the code in a folder
        cd current/ && make stop \                          # stop the current server
        cd ../ && rm current/ && ln -s $(TAG)/ current/ \   # move the symbolic link to the new version
        cd current/ && make start \                         # restart the server
    "
    echo 'Deployed $(TAG) version to $(SERVER)' 
```

Enter fullscreen mode Exit fullscreen mode

我使用环境变量来指定我想要部署的标签:

```
$ TAG=4dd370f make deploy
> Deployed 4dd370f version to proxy-staging 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，部署实际上非常快，因为我不需要在目标环境中*构建*。

提示:这意味着如果构建包含二进制文件，它们必须针对目标环境进行编译。简单来说，这意味着你应该*在你*运行*代码的同一个系统上*开发你的代码。一旦你使用了流浪者或码头工人，听起来就更简单了。

**提示:**我使用了没有任何凭证参数的`ssh`命令。你不想在`makefile`中保存这些凭证，所以我建议使用你当地的`~/.ssh/config`来保存它们。这样，您可以安全地与您的同事共享它们，并将这些凭证保存在存储库之外——同时在 Makefile 中有部署指令。下面是我的`proxy-staging` :
的 SSH 配置示例

```
Host proxy-staging
    Hostname staging.domain.me
    User ubuntu
    IdentityFile ~/.ssh/keys/staging.pem 
```

Enter fullscreen mode Exit fullscreen mode

### 部署到多个环境

服务器现在可以在`proxy-staging`服务器上运行，使用默认配置。如果我想将相同的构建部署到一个`proxy-production`服务器，但是使用不同的端口，该怎么办？

我所需要的就是确保无论代码在哪里运行，环境变量`NODE_PORT`都被设置为正确的值。

有很多方法可以实现这个**配置**。

最简单的方法是将环境变量的值直接写入每个服务器的一个`~/.ssh/environment`文件中。这样，我不需要记住如何或何时检索配置:每次我登录机器时，它都会自动加载。

```
> ssh production-server "echo 'NODE_PORT=8000' >> ~/.ssh/environment"
> ssh production-server "echo 'PROXY_HOST=https://host.to.proxy' >> ~/.ssh/environment"
> ssh production-server
> env NODE_PORT=8000
PROXY_HOST=https://host.to.proxy 
```

Enter fullscreen mode Exit fullscreen mode

现在我可以将我在`proxy-staging`中使用的工件部署到`proxy-production`中，不需要重新构建。

```
$ TAG=4dd370f SERVER=proxy-production make deploy
> Deployed 4dd370f version to proxy-production 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。

**提示:****回滚**就像部署前一个工件一样简单。

在这一点上，自动化过程是很容易的:让 CI 在每次合并 PR 时或者在每次推动 master 时构建一个工件(Travis、Jenkins 和其他 CI 允许实现一个构建阶段)。然后，用一个特定的标签将这个构建存储在某个地方，比如 commit hash 或者 release 标签。

当有人想要部署时，他们可以在服务器上运行一个脚本来下载工件，通过环境变量对其进行配置，然后运行它。

**提示:**如果你不想在你拥有的每台生产服务器上写你的环境变量，你可以使用一个**配置管理器**，比如 [comfygure](https://github.com/marmelab/comfygure) 。声明:是我们写的！

## 什么时候应该使用基于工件的部署

基于工件的部署有很多优点。它可以快速部署，即时回滚，备份可用并随时可以运行。

它允许在每个环境中运行完全相同的代码。事实上，对于工件，部署变成了一个*配置*问题。如果一堆代码在试运行时工作，没有理由在生产时失败，除非配置中有错误——或者环境不一样。因此，投资一个严格等同于生产环境的试运行环境是一个好主意。

在这样的部署过程中，特性标记也更容易:只需检查环境变量。最后但同样重要的是，部署可以自动化到非技术人员(如产品负责人)也可以完成的程度。我们做到了，我们的客户也很喜欢。

但是自动化这样一个过程需要很高的成本。安装和维护需要时间，因为当构建过程不仅仅是压缩几个文件时，你需要用 Webpack 这样的*捆绑器*来运行它。此外，还要考虑存储工件和备份所需的额外磁盘空间。

**提示:**使用环境变量，不要检查环境。尝试在代码中找到`if (env !== 'production')`的实例，并用一个重要的环境变量替换它们，比如`if (process.env.LOGGING_LEVEL === 'verbose')`。

概念验证、早期项目或独立开发人员不需要这样的部署过程。所有的优势都来自一个团队在一个成熟的项目上的工作。

问问自己在部署上花了多少时间，看看这个图表！

[![xkcd: Is it worth the time?](img/09bb69c0bfcd8f3864aea07de038c826.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--a2YLrk7w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgs.xkcd.com/comics/is_it_worth_the_time.png)

## 常见问题

我的项目是 SPA / PWA。我必须在构建阶段配置我的包！

你不需要。在这个过程的后面，有许多方法可以配置单页应用程序。例如，在运行时从不同于其他静态资产的位置加载一个`config.json`。或者，用服务器端渲染写个`window.__CONFIG__`。

不要害怕显示前端配置，这将更容易调试。如果你在你的前端配置中有敏感的信息隐藏在你的缩小的和神秘的 webpack 版本中，你已经做错了。

数据库迁移怎么样？

应用程序部署时，迁移不必运行。他们应该有自己的管道，可以在适当的时候触发。这样，您可以单独处理每个部署，并独立回滚其中的任何一个。这显然意味着要做大量备份，并且要有可恢复的迁移。

如果有大的或者痛苦的迁移，不要犹豫，一步一步来。例如，如何移动一个表:首先创建新表，然后复制数据，最后在三个不同的迁移中删除结果表。可以在复制和删除表之间部署应用程序。如果出现问题，应用程序可以快速恢复，而无需接触数据库。

## 结论

正确安装该流程后，部署将变得更快、更安全。它让团队和产品所有者对部署更有信心。

基于工件的部署是一种强大的技术，当项目接近成熟时，它确实值得考虑。它与敏捷方法如 SCRUM 完美匹配，甚至与看板更匹配，并且是持续交付的先决条件。

为了进一步了解部署技术，我推荐 Nick Craver 的大型文章: [Stack Overflow:我们如何进行部署- 2016 版](https://nickcraver.com/blog/2016/05/03/stack-overflow-how-we-do-deployment-2016-edition/)。