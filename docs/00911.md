# 如何快速将 VueJS 应用部署到 Heroku

> 原文：<https://dev.to/jmbejar/how-to-quickly-deploy-a-vuejs-app-to-heroku-5b0f>

最初发表于 [WyeWorks 博客](https://wyeworks.com/blog/2018/1/8/how-to-quickly-deploy-a-vuejs-app-to-heroku/)。

最近，我花了一些时间学习 VueJS，我发现这是一个非常有趣的框架。事实上，过去几天我一直在做一个新的项目原型，想给一些人看看，所以我想把它发布在互联网上。

我决定在 Heroku 上部署这个项目，所以我开始研究最好的方法是什么。令我惊讶的是，除了一些帖子之外，我没有找到太多关于它的信息，比如[Quick-n-clean way to deploy Vue+web pack apps on Heroku](https://codeburst.io/quick-n-clean-way-to-deploy-vue-webpack-apps-on-heroku-b522d3904bc8)和[easy deploy a Vue+web pack App to Heroku in 5 Steps](https://medium.com/netscape/deploying-a-vue-js-2-x-app-to-heroku-in-5-steps-tutorial-a69845ace489)。尽管如此，我最终得到了一个不同的设置，这也是这篇文章的主题。

假设已经创建了一个 Heroku 帐户，并且 VueJS 项目已经存在，我在研究中发现的上述文章中解释的方法可以总结为以下步骤:

*   使用 Express 编写一个最小的 NodeJS web 服务器
*   在本地构建资产
*   将`dist`文件夹添加到 Git 存储库中，以便在推送到 Heroku 时包含它

我不喜欢的是这些解决方案需要在本地构建站点，并在`dist`文件夹中签入更改。我想让 Heroku 在推出我的应用程序的新版本时处理这个步骤。

## 我们的解决方案

让我们假设我们有一个 VueJS 项目，它是使用带有`webpack`模板的`vue-cli`生成的。澄清一下，这个项目是使用下面的命令创建的:

```
vue init webpack <YOUR-PROJECT-NAME-HERE> 
```

Enter fullscreen mode Exit fullscreen mode

当然，我们还需要一个 Heroku 帐户和一个在那里创建的新应用程序。Heroku 将使用 NodeJS buildpack，因为我们的项目在根文件夹中包含一个`package.json`。

### 第一步:添加一个最小 NodeJS 服务器

这是借用前面提到的博文的一个步骤。我们必须在项目的根文件夹中添加一个包含以下代码的`server.js`文件:

```
const express = require('express');
const path = require('path');
const serveStatic = require('serve-static');

let app = express();
app.use(serveStatic(__dirname + "/dist"));

const port = process.env.PORT || 5000;
app.listen(port, () => {
  console.log('Listening on port ' + port)
}); 
```

Enter fullscreen mode Exit fullscreen mode

由于这段代码使用了 Express，我们需要将这个依赖项添加到我们的项目中:

```
npm install express --save 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过运行以下命令在本地测试该服务器:

```
npm run build
node server.js 
```

Enter fullscreen mode Exit fullscreen mode

### 第二步:设置 package.json 脚本

我们需要调整`package.json`中的`scripts`部分。如果 Vue Webpack 模板提供的包文件没有被修改，它应该包括两个重要的任务，`start`和`build` :

```
"scripts":  {  ...  "start":  "npm run dev",  ...  "build":  "node build/build.js"  }, 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，Heroku 将执行`start`脚本来启动服务器。出于这个原因，我们将更改与`start`相关的命令来运行我们的定制服务器脚本:

```
"scripts":  {  ...  "start":  "node server.js",  ...  }, 
```

Enter fullscreen mode Exit fullscreen mode

请注意，您不能再使用`npm run start`在您的计算机上运行开发服务器。我决定直接使用`npm run dev`,但是您可以在`scripts`部分添加一个新条目，并为其添加一个别名。

我们仍然需要添加一些东西来确保每次部署代码时,`dist`文件夹都构建在我们的 Heroku 实例中，否则服务器脚本将无法正常工作。我们将使用一个名为`heroku-postbuild`的特殊脚本，在这里记录为。我们的想法是使用这个特殊的钩子来建立这个网站，所以让我们把它添加到我们的`package.json` :

```
"scripts":  {  ...  "heroku-postbuild":  "npm install --only=dev --no-shrinkwrap && npm run build",  }, 
```

Enter fullscreen mode Exit fullscreen mode

让我们解释一下这个命令。首先，我们需要安装用于构建资产的依赖项。在用 Webpack 模板创建的 VueJS 项目中，所有需要的依赖项都在`devDependencies`中，所以我们必须添加`--only=dev`选项。

`—no-shrinkwrap`选项用于避免在安装过程中与 Heroku 安装的包发生冲突(安装生产依赖项的地方)。然而，在大多数情况下，这可能是一个不必要的选择。

当然，在服务器启动之前，我们正在运行`npm run build`来实际构建站点。

### 第三步:尝试，享受！

我们现在准备部署到赫罗库。假设我们已经有了一个 Git 存储库，我们需要添加 Heroku 远程 repo:

```
heroku git:remote -a <YOUR-HEROKU-APP-NAME-HERE> 
```

Enter fullscreen mode Exit fullscreen mode

部署我们的应用程序的命令是:

```
git push heroku master 
```

Enter fullscreen mode Exit fullscreen mode

它将推送代码，触发构建步骤并启动 NodeJS 脚本，该脚本将为我们用 VueJS 创建的站点提供服务

## 讨论

关于在 Heroku 中构建一个步骤而不是签入到`dist`文件夹中的决定，可能会有一些讨论。在本地构建站点会使 Heroku 的设置不那么复杂，因为我们可以假设`dist`文件夹总是存在的。然而，将`dist`文件夹放在我们的 Git 存储库中似乎不是一个好的做法，因为这将使得读取提交更改和处理合并冲突变得更加困难。此外，团队中的每个开发人员都需要一些努力和纪律来保持存储库中资产的正确构建版本。出于所有这些原因，我们倾向于将网站构建为部署过程中的一个自动化步骤。

说到`heroku-postbuild`钩子，有些人实际上正在使用`post-install`，它似乎也适用于 Heroku。这个 npm 钩子的目的是在安装包时被调用，在我看来，它应该在库项目的上下文中使用，而不是在应用程序项目中。我宁愿使用 Heroku 提供的最具体的钩子。

关于运行`heroku-postbuild`钩子中的`npm install`来安装我们的`devDependencies`的需要，我们可以讨论几个可用的替代方案来解决这个问题:

### 不使用`devDependencies`

最简单的方法是将所有东西都移到`dependencies`中，根本不使用`devDependencies`。
事实上，我正在比较用 [create-react-app](%5BGitHub%20-%20facebookincubator/create-react-app:%20Create%20React%20apps%20with%20no%20build%20configuration.%5D(https://github.com/facebookincubator/create-react-app)) 创建的 React 项目与 Heroku 的部署过程，并意识到构建网站所需的所有脚本和依赖项实际上都在`dependencies`部分。在这种情况下，这就是您在`package.json`文件中找到的内容(`react-scripts`包包含了用于构建站点的所有依赖项):

```
 "dependencies":  {  "react":  "^16.2.0",  "react-dom":  "^16.2.0",  "react-scripts":  "1.0.17"  },  "scripts":  {  "start":  "react-scripts start",  "build":  "react-scripts build",  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

注意，那里没有`devDependencies`。因此，在`heroku-postbuild`钩子中运行`npm run build`是安全的，因为在 Heroku 的默认部署过程中，构建站点所需的所有包都已经安装好了。

在任何情况下，我认为在`dependencies`和`depDependencies`这两个类别中保持良好的依赖关系是一个很好的实践。因此，我们选择在`heroku-postbuild`钩子步骤中加入额外的`npm install`，而不是改变`vue-cli`提供的默认配置。

### 将 NPM _ 配置 _ 生产设置为假

将环境变量`NPM_CONFIG_PRODUCTION`设置为`false`会导致来自`devDependencies`的包也将默认安装在 Heroku 的部署过程中。默认值是`true`，因为最常见的情况是只安装`dependencies`列表中的项目。

调整这个值并让`heroku-postbuild`脚本只运行`npm run build`将是一个有效的解决方案。即便如此，注意这种变化也会影响`NODE_ENV`的值，正如这里的[和](https://devcenter.heroku.com/articles/nodejs-support#configuring-npm)所解释的那样。这有可能在构建过程中引起一些副作用，但是据我所知，使用 VueJS 项目的默认 Webpack 配置，这不太可能发生。

* * *

希望这篇文章对你有用，并让你的 VueJS 项目最终部署到 Heroku！如果您对以下步骤有任何问题，请留下您的评论，这样我们可以一起找到解决方案并改进这篇文章。