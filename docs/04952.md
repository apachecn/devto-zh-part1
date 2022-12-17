# 理性和反应-本地

> 原文：<https://dev.to/kayis/reason--react-native>

*编辑(2017 年 12 月 15 日)*

现在有一种更简单的方法:

[https://github . com/react-community/reason-react-native-scripts](https://github.com/react-community/reason-react-native-scripts)

* * *

在我的上一篇关于原因的帖子之后，我花了一些时间再次尝试。学习一门新语言需要时间。你必须获得新的语法、语义和工具。我是一个更实际的学习者，尽管我喜欢日复一日地阅读新技术，但我知道直到我自己尝试了，我才真正得到东西，事情总是与创造者告诉你的不同。

无论如何，几天前我读到有人试图找到使用 React-Native 的理由，因为 React-Native 是我目前的事情，所以我想尝试一下。

# 什么

我会告诉你如何带着反应和理智开始行动。我会用 Create-React-Native-App，因为直接用 React-Native 有点麻烦。所以不需要 Mac 或 75 TB Android SDK！

要求: **Linux 和一个 Android 或 iOS 设备。**

这应该也适用于 MacOS。

# 如何

首先安装[节点版本管理器](https://github.com/creationix/nvm)。

```
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.4/install.sh | bash 
```

Enter fullscreen mode Exit fullscreen mode

然后用它安装 Node.js 7 *(Node 8 自带 npm 5，不支持 create-react-native-app)*

```
nvm install 7 
```

Enter fullscreen mode Exit fullscreen mode

下一步是安装 [Create-React-Native-App](https://github.com/react-community/create-react-native-app) 。

```
npm i create-react-native-app -g 
```

Enter fullscreen mode Exit fullscreen mode

并用它来创造...一款 React-Native 应用:D

```
create-react-native-app my-awesome-app 
```

Enter fullscreen mode Exit fullscreen mode

**可能出现的一些问题:**

`react-native-scripts`缺失，安装它:

`npm i react-native-scripts -D`

监听文件更改的进程需要一些额外的配置，所以它可以*监视所有的文件！*

这在您重新启动系统之前一直有效:

```
sudo sysctl -w fs.inotify.max_user_instances=1024
sudo sysctl -w fs.inotify.max_user_watches=12288 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个常规的基于 JavaScript 的 React-Native 应用程序，我们可以用`npm start`启动它，并获得一个我们需要以某种方式扫描的二维码。

我们使用移动设备安装 **Expo 客户端**。

对于 iOS 系统来说，你可以从 T2 的应用商店获得

对于安卓系统，你可以从 T2 的 Google Play 获得

世博客户端让你扫描二维码，并打开之前创建的应用程序。

这一切都很好，但是我们来这里是有原因的！

让我们在项目中添加一些原因代码。

我更喜欢把我的应用程序代码放在一个`modules`文件夹里。所以在你的`my-awesome-app`目录中创建一个，然后放一个`app.re`在里面！

`my-awesome-app/modules/app.re`应该有这样的内容:

```
open ReactNative;

let app = () => 
  <View style=Style.(style([flex(1.), justifyContent(`center), alignItems(`center)]))>
    <Text value="Reason is awesome!" />
  </View>; 
```

Enter fullscreen mode Exit fullscreen mode

一个简单的函数，返回一些元素，没什么特别的。

但是我们的应用程序现在不做任何事情，所以我们需要为构建设置一个工具链。

为此，我们需要在我们的项目中添加一些`devDependencies`。

```
npm i bs-platform reason-react bs-react-native -D 
```

Enter fullscreen mode Exit fullscreen mode

安装时会编译 OCaml 编译器，这可能需要一些时间。

`bs`代表 [BuckleScript](https://bucklescript.github.io/bucklescript/Manual.html) ，这是我们将要使用的 OCaml 编译器的后端。它可以把 OCaml 和 Reason 编译成 JavaScript。像[巴别塔](https://babeljs.io)可以把 ES2015/16/17 的花哨功能编译成老派的 JavaScript。

`bs-platform`需要在您的`my-awesome-app`目录中有一个`bsconfig.json`并进行一些配置才能运行:

```
{  "name":  "my-awesome-app",  "reason":  {  "react-jsx":  2  },  "bsc-flags":  ["-bs-super-errors"],  "bs-dependencies":  ["bs-react-native",  "reason-react"],  "sources":  [{  "dir":  "modules"  }]  } 
```

Enter fullscreen mode Exit fullscreen mode

对我们来说最重要的是`dir`和`name`。

我们还需要给`package.json`添加一些`scripts`。

```
"build": "bsb -make-world -clean-world",
"watch": "bsb -make-world -clean-world -w" 
```

Enter fullscreen mode Exit fullscreen mode

因为我们安装了`bs-platform`作为`devDependency`，`bsb`命令将可用于这些脚本。

我们现在可以通过运行以下命令将`app.re`编译成`app.js`

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

你会在`my-awesome-app/lib/js/app.js`找到它

Create-React-Native-App 期望在您的`my-awesome-app`目录中有一个`App.js`，它将 React 组件导出为`default`，但是我们的组件既不在正确的文件中，也不是默认导出。所以我们需要使用`App.js`来连接推理世界和 JavaScript 世界。

打开`my-awesome-app/App.js`,将其内容替换为:

```
import {app} from "./lib/js/modules/app";
export default app; 
```

Enter fullscreen mode Exit fullscreen mode

终于一切就绪。

使用以下命令启动 React-Native dev 服务器:

```
npm start 
```

Enter fullscreen mode Exit fullscreen mode

用你的移动设备和世博会客户端扫描提供的二维码，它会向你显示“理由太棒了！”

稍后你可以在运行`npm start`的时候用`npm run watch`代替`npm run build`，这样当你改变一些东西的时候，所有的东西都会被编译。

# 奖金

如果你想在开发时使用所有 Reason 的优秀特性，我推荐你在 Visual Studio 代码中使用 [Reason CLI](https://github.com/reasonml/reason-cli) 。

在 Linux 上，通过 npm 安装 CLI

```
npm install -g https://github.com/reasonml/reason-cli/archive/beta-v-1.13.6-bin-linux.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

然后安装 [VSCode 原因插件](https://marketplace.visualstudio.com/items?itemName=freebroccolo.reasonml)。

要使用 refmt 获得良好的自动格式化，您需要将`"reason.formatOnSave": true`添加到 VSCode 配置中。

## 总结起来

所以我认为从理性和反应开始并不太难。Linux 的开发比在 MacOS 上感觉更笨拙，并且总体上 React-Native 在 Linux 上比在 MacOS 上慢得多，但这是 React-Native 或者甚至可能是 Create-React-Native-App 的问题。

在这样一门年轻的语言中，缺少的，并且预计会缺少的，是绑定和/或库。我在 React-Native 中使用[魅力-native](https://github.com/robinpowered/glamorous-native) 来描述我的风格，这真的感觉像是从写类似于:

```
<View backgroundColor="red" width="100%"> 
```

Enter fullscreen mode Exit fullscreen mode

写这篇文章:

```
<View style=Style.(style([backgroundColor("red"), widthPct(100.)]))> 
```

Enter fullscreen mode Exit fullscreen mode

造型是一个前端开发的面包和黄油，所以我不知道我能不能接受，哈哈，但自动完成当然很好。

对于这样一个非常年轻的项目，我认为这仍然是一个非凡的成就，值得一试。

此外，如果你在设置 React-Native 时需要一些视觉帮助，我还在 [Skillshare](http://skl.sh/2tRgwpE) 上制作了一个视频课程，可能会对你有所帮助。

感谢[肯·惠勒](https://twitter.com/ken_wheeler)，他写了一个 [React-Native 应用，我以此作为这篇文章的基础。](https://github.com/FormidableLabs/seattlejsconf-app)