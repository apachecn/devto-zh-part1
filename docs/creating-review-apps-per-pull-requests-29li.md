# 根据拉取请求创建审核应用程序

> 原文：<https://dev.to/caduribeiro/creating-review-apps-per-pull-requests-29li>

[![](img/4ba724eaf4b39d42d79e71c2840ba95a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--UmYjfts6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/300/1%2AJKrc7lJZGa3_R1jA8yXxuQ.png)

在这篇文章中，我将展示一个简单的例子，关于如何为每个拉请求创建应用程序(或者，创建你自己的 Gitlab/Heroku Review 应用程序)。

让我们想象一下开发团队的以下场景:

*   开发者创建一个新的特性分支。
*   开发商推分店
*   开发人员打开一个拉请求，这样其他开发人员就可以检查他的代码并测试该特性
*   CI 运行测试，如果通过，则分支变为绿色

现在，开发人员应该将该特性发送给其他人进行测试。该特征不应被合并到主特征中，以便可以对其进行测试。它们应该被隔离测试，因为一个特性可能会干扰另一个特性，而 master 应该只有可部署的代码。考虑到这一点，我们将使用 Docker 为每个 pull 请求创建一个环境。

我将使用 Jenkins 作为 CI，因为我想使用一个开源工具来演示这一点，但是您可以使用任何您喜欢的 CI 工具。

这篇文章假设你已经安装了 Jenkins。我将用[这个](https://github.com/opensanca/opensanca_jobs)开源 Rails 应用程序作为例子。将此回购转入您的帐户，并在您的计算机中复制它。

```
git clone [https://github.com/yourgithubusername/opensanca\_jobs.git](https://github.com/duduribeiro/openjobs_experiment.git) 
```

你可以得到我在 https://github.com/duduribeiro/openjobs_jenkins_test 使用的完整代码回购

### 配置 Docker

我们需要的第一步是用 docker 配置我们的应用程序。

Dockerfile 是一个文件，它包含构建包含我们的应用程序的图像的指令。你可以在他们的[文档](https://docs.docker.com)中了解更多关于 Docker 的信息。

在 app 文件夹中创建一个名为 Dockerfile 的文件: