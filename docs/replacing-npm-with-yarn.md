# 用纱线代替 npm

> 原文：<https://dev.to/bugsnag/replacing-npm-with-yarn>

这篇文章最初发表在 [Bugsnag 博客](https://blog.bugsnag.com/replacing-npm-with-yarn/)上。 [Bugsnag 的实时错误监控](https://bugsnag.com/)平台是为您的用户提高软件质量最实用的方式。通过了解生产错误以及用于复制和修复的诊断数据，开发人员可以改善客户体验和代码质量。

抓紧你的毛衣。烘干机里有一个全新的工具。周二，脸书宣布了一个新的 Javascript 包管理器，它将取代 NPM。

最初的本能反应可能是对分叉基本工具的概念感到紧张。然而，yarn 的工作方式与现有的基础设施兼容。它仍然使用现有的 npm 注册表，其 CLI 与`npm`的 CLI 基本相同。

Bugsnag 前端团队如坐针毡，主要是因为我们经常被不同开发人员的机器之间的依赖性变化所困扰。我们经常不得不删除我们的 node_modules 文件夹，重新安装一切来解决神秘的问题。我们以前尝试过 npm 的包膜功能，但是，像脸书一样，它过时了，所以我们一直渴望一个替代的解决方案。

Yarn 通过使用自动更新的“锁定文件”将依赖关系绑定到特定版本来解决这个问题。在这个文件被提交到源代码控制之后，我们将永远不必担心机器之间不一致的依赖关系或者随时间的变化。

## 从 npm 移动到纱线

迁移过程非常简单。只需运行`yarn`命令并提交生成的`yarn.lock`文件。然后 grep 我们所有的 docker 和 deploy 脚本来引用`npm`，并用 yarn equivalents 替换它们。

## 性能提升

一些初步的粗略基准看起来很有希望。只是想让你知道羊毛没有蒙住你的眼睛，这里是原始数据。

[![Yarn install](img/32f5695af985b588158ba91443e3d7fe.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--w8O4sRl7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://blog.bugsnag.com/img/posts/yarn-install.gif)

| 命令 | 时间(秒) |
| --- | --- |
| `npm install` | 155 秒 |
| `yarn`(冷缓存) | 53s |
| `yarn`(热缓存) | 13s |

Yarn 将缓存依赖关系，因此后续安装无需重新下载即可完成。这甚至在没有互联网连接的情况下也能工作。

## 纱有线

即使没有缓存，由于并行化，Yarn 也更快。

我对它将对 CI 服务器产生的影响感到特别兴奋，在 CI 服务器中，我们为每个构建从一个新的 node_modules 文件夹开始。

## API

除了几个明显的例外，API 基本相同。使用 NPM，默认的`npm install <some-package>`命令不会将依赖项添加到`package.json`，而`yarn add <package-name>`会。我认为这是更好的默认行为，因为这种选择很容易忘记提交依赖关系变更。

Yarn 还增加了几个额外的功能。`yarn why`命令，它将显示哪个依赖项导致了一个包的下载。

[![yarn why command](img/bb969518052e5e99dc4e724e02212bd3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xVBBmOZX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.bugsnag.com/img/posts/yarn-why.png)

## 缝得多了

这只是对纱线的一些初步印象。还有许多我没有涉及到的特性。查看[官方网站](https://yarnpkg.com/)了解更多信息，包括安全性增强、性能特性和扁平模式。