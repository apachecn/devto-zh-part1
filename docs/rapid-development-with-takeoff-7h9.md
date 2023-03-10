# 起飞快速原型

> 原文：<https://dev.to/tanepiper/rapid-development-with-takeoff-7h9>

[Takeoff](https://takeoff.sh) 是一个快速开发应用程序的工具包。我在 2017 年 8 月开始开发它，此前我一直在从事一个项目，为游戏[精英:危险](https://www.elitedangerous.com/)读取和显示日志。

当我设置应用程序时，我读过文章 [Dockerize 你的应用程序，并保持热重装！](https://blog.bam.tech/developper-news/dockerize-your-app-and-keep-hot-reloading)我用这种方式创建了环境，这样我就可以将后端和前端分开，而不必在我的主环境中安装`mysql`。

我从来没有真正抽出时间来完成那个应用程序，但是当我开发它的时候，我意识到我有了一个可以重用的`docker-compose`应用程序的良好布局。这是我开发的第一个 takeoff 版本，它是一组非常固执己见的命令行，需要从 Github 克隆环境。

然后我休息了两周去澳大利亚度蜜月。当我回来时，我意识到我可以将应用程序从命令中分离出来，并构建一个工具包，该工具包可以轻松地用于其他应用程序库。

我称这些蓝图为动力输出。默认情况下，安装的第一个蓝图是[基本蓝图](https://github.com/takeoff-env/takeoff-blueprint-basic)，它包括一个包含电池的应用程序。你得到了基于节点的 API、React 前端应用程序和 Postgres 数据库——所有这些都在 docker 中运行，并在端口 80 上有一个 Ngnix 代理。完整的详细信息在自述文件中。

这些应用程序包括基本的用户管理和认证，并通过 JSON Web 令牌(JWT)进行通信，因此您可以直接开始编写应用程序。

## 入门

你可以通过 npm: `npm install -g @takeoff/takeoff`轻松安装。

一旦安装了命令行工具，您就可以创建您的第一个环境:

```
takeoff init my-new-app
cd my-new-app 
```

Enter fullscreen mode Exit fullscreen mode

这创建了你的第一个环境，在这里你会找到两个文件夹- `blueprints`和`envs`。`blueprints`文件夹是一个本地缓存，而`env`文件夹是你可以找到这些项目的源文件的地方，在你的`envs/default`文件夹中——在这里你可以编辑文件来添加你的项目特性——并且你的应用程序将一直在`http://localhost`上运行，并在你做出改变时热重装。

键入以下命令开始:

```
takeoff start 
```

Enter fullscreen mode Exit fullscreen mode

是的，获得一个完全工作的开发环境真的很容易。

在 Takeoff 的 envs 文件夹中，每个应用程序都有自己的文件夹和结构。API 使用通过配置加载的[哈比神](https://hapijs.com/)插件，您还可以访问[序列](http://docs.sequelizejs.com/)和 CLI 来运行数据库迁移和种子阶段。

在 React 应用程序中，你会发现文件被分解成多个组件，每个组件都有自己的视图和存储，一个应用程序就能把它们放在一起。在未来，我还将提供一个 Angular 应用程序，以及一个使用普通 web 组件的应用程序。

如果您有任何反馈或发现任何问题，请前往 [Github 页面](https://github.com/takeoff-env/takeoff)留下问题。