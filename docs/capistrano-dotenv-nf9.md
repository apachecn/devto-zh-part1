# 卡皮斯特拉诺-多滕夫

> 原文：<https://dev.to/jplethier/capistrano-dotenv-nf9>

dotenv 是为 ruby on rails 项目加载 env 变量的一种非常方便的方式。将 env 变量放在一个文件中很容易。env ),并在 rails 服务器启动时从该文件加载。我已经在很多项目中使用这个 gem 和 [capistrano](https://github.com/capistrano/capistrano) 作为部署工具。向 capistrano 添加一个任务来读取一个名为。env_production 并将其作为. env 文件上传到您的生产服务器，当您的 rails 服务器在生产中启动时，该文件将用于从 dotenv gem 中读取和加载您的所有 env 变量。

但是当您在具有这些配置的项目中工作时，当您向代码中添加一个新的 env 变量而忘记在 capistrano 使用的 production dotenv 文件中为这个 env 变量添加一个值时，通常会面临一个问题。因此，您的项目在部署后崩溃，当您检查错误时，您需要做的只是为使用的新 env 变量添加一个值，并进行新的部署来修复。

<figure>[![](img/784d52e657fc0369c911583c77dfdca2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w7T-CF_p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ArjONKQGtgIt-YzOCAcASmQ.jpeg) 

<figcaption>你是不是也忘了定义一个 env var？</figcaption>

</figure>

前一段时间我面对这个问题不是第一次，足以让我想找到一个解决办法。我开始寻找一个工具，如果在项目代码中使用了一些未定义的 env 变量，就停止 capistrano 部署，因为我没有找到任何工具，所以我决定为这项工作构建一个简单的 gem。

capistrano-dotenv 非常简单，它主要向 capistrano 添加了三项任务，因此您可以从一个文件中读取所有 env 变量，检查您的项目代码中使用的所有 env 变量是否都已定义，并在您的远程服务器中进行设置，将其上传到一个. env 文件中，该文件将在 dotenv gem 中使用。

要读取所有的 env 变量，只需将 invoke 'dotenv:read '添加到 capistrano 部署任务中。默认情况下，这个任务将从一个. env 文件中读取所有的 env 变量，但是您可以[设置一个自定义的 env 文件](https://github.com/jplethier/capistrano-dotenv#env-file)。

在此之后，如果您想确保在进行部署时不会忘记定义任何 env 变量，只需添加 invode 'dotenv:check '，它将在整个 rails 项目中搜索任何 env['SOMEVAR']和 ENV.fetch('SOMEVAR ')以找到所有使用的 ENV 变量，然后将找到的列表与之前读取的 ENV 文件中定义的变量进行比较。如果该任务在代码中找到了任何人，但在 env 文件中没有找到，那么部署将失败，并在终端中输出所有缺少定义的 env 变量。

因为有时我们添加一些只在开发中使用的 env 变量，或者只在暂存中使用而不在生产中使用，有时 env 变量只在本地使用，所以有可能[设置一个可选的](https://github.com/jplethier/capistrano-dotenv#optional-env-vars)列表和一个被忽略的 env 变量的[列表。对于可选的 env 变量，如果在代码中发现了这个列表中的某个变量，但在 env 文件中没有，那么部署不会失败，但是它会在终端中询问您是否希望在不定义这个变量的情况下继续部署。](https://github.com/jplethier/capistrano-dotenv#ignored-env-vars)

因此，我构建的这个 gem 与我在 github 中找到的其他一些检查工具之间的最大区别是，我想要一个解决方案，我不需要记住将新的 env var 添加到检查变量列表中，我想要在我的代码上添加新的 env var 后，它将被自动检查，而不需要记住任何东西。因此，我宁愿因为忘记将可选的 env 变量添加到可选列表而导致部署失败，也不愿因为忘记定义必需的 env 变量并忘记将其添加到必需的 env 变量列表而导致项目在生产中崩溃。

因此，代码是开源的，可以在 [my github](https://github.com/jplethier/capistrano-dotenv) 上获得，任何建议和贡献都很好。任何反馈。