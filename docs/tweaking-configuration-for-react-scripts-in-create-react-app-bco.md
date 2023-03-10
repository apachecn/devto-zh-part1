# 调整创建 React 应用程序中 React 脚本的配置

> 原文：<https://dev.to/shubheksha/tweaking-configuration-for-react-scripts-in-create-react-app-bco>

我最近在 [Create React App](https://github.com/facebookincubator/create-react-app/) (CRA)上做一个[问题](https://github.com/facebookincubator/create-react-app/issues/753)，为此我需要了解 CRA 的基本控制流程。[丹·阿布拉莫夫](https://medium.com/u/a3a8af6addc1)非常友好地向我详细介绍了正在发生的事情&我认为如果我能尽我所能写完这篇博文中的所有信息，这对潜在的投稿人会很有用&也会省去他很多麻烦。

CRA 的所有肉主要生活在一个叫做 [react-scripts](https://github.com/facebookincubator/create-react-app/tree/master/packages/react-scripts) 的包里。它作为一个依赖项安装在应用程序的 node_modules 文件夹中。这个包包含了构建、测试和启动你的应用程序的脚本。[bin 脚本](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/bin/react-scripts.js)负责调用各自的脚本。当您在应用程序的根文件夹中安装 npm 时，npm 会在 node_modules/中创建一个符号链接。将文件夹绑定到 react-script.js，以便可以从 CLI 直接使用它。[使用 CRA 生成的项目使用这些脚本](https://github.com/reactjs/redux/blob/ec20b4a6c39301db884df68d3b9bd48a90e25946/examples/counter/package.json#L17-L20)来测试，运行&构建应用程序。

有几种方法可以使用 CRA 生成 React 项目:

#### #1。默认设置下的零配置

CRA 的全部目的是帮助开发没有预定义配置的 React 应用程序，以便开发人员可以主要关注应用程序的“React”部分，而不是花费几个小时学习和研究各种可用的配置选项&仍然不能最终得到满足他们需求的东西。记住这一点，CRA 主要是不可配置的。使用这种方法的好处是上游存储库中所有对 react-scripts 的更新可以很容易地集成到您的项目中。这代表了一个极端。

#### #2:使用弹出

另一种极端的方法是调整 CRA 的配置。

*   这个方法可以通过运行 npm run eject 来使用，它在 react-scripts 包中执行一个名为 [eject.js](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/scripts/eject.js) 的脚本。
*   这将完全移除项目中的 react-scripts 依赖项，并将所有脚本和配置文件作为独立的依赖项(如 Babel、Webpack 等)复制到项目中。
*   应用程序的 package.json 也被重写，因为 react 脚本不再存在&因此，为了能够运行、测试和构建应用程序，我们将所有这些脚本放在 scripts/中，以便它们可以在 package.json 中的 scripts 对象中被引用
*   这样做的主要缺点是，一旦退出脚本，就无法撤销&您只能靠自己管理自己的依赖项和配置文件，无法利用随时间推移对 react-script 进行的更新

#### #3:中间地带——使用叉子

如果您只想稍微调整一下您的 CRA 配置，那么弹出您的脚本是一个极端的措施，有很多缺点。另一个替代方法是创建自己的 react 脚本分支&在应用程序中使用它。这样做的步骤如下:

*   Fork react-scripts &调整配置以满足您的需求。让我们把新的 fork 称为 react-scripts-fork。[更改](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/package.json#L2) [的 package.json 中的名字 react-scripts](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/package.json#L2)
*   [将您的 react-scripts-fork 发布到 npm](https://docs.npmjs.com/getting-started/publishing-npm-packages)
*   使用创建您的 react 应用程序

```
$ create-react-app my-app --scripts-version react-scripts-fork 
```

*   这将把经过调整的反应脚本安装到应用程序的 node_modules/目录中
*   像 npm 启动、npm 测试、npm 运行构建这样的所有其他工作都像使用现成的 react 脚本一样
*   这种方法的好处是，您并不是独自一人，因为配置和依赖关系到处都是
*   缺点是您需要自己维护它&手动合并来自上游的更改

作为一个例子，你可以看看名为 [的](https://github.com/kitze/create-react-app) [npm 包，它解决了大多数常见的对 CRA 所缺少的东西的请求。](https://github.com/kitze/create-react-app)

又及——上面写的大部分都是丹的话&我只是试图为我所理解的一切创造一个要点。希望这有所帮助！

*本帖最初发表于[medium.com](https://medium.com/@shubheksha/tweaking-configuration-for-react-scripts-in-create-react-app-d91e9d03a42f)T3】*