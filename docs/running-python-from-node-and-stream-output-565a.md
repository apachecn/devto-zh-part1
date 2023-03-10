# 从节点和流输出运行 python

> 原文：<https://dev.to/ruiclarateixeira/running-python-from-node-and-stream-output-565a>

我目前的副业项目是[RPiCon](https://ruiclarateixeira.github.io/rpicon/preact/photon/electron/raspberrypi/2017/12/17/rpicon-i.html)——一个为 raspberry pi 开发 python 的桌面应用程序。

主要特性是，在利用虚拟 GPIO 部署到 pi 之前，您将能够在您的台式机/笔记本电脑上运行代码。因为我已经决定把它写成一个电子应用程序，所以我需要一种方法来运行来自 [nodejs](https://nodejs.org/en/) 的 python 代码并与之交互。

在决定如何运行代码时，我有两个要求:

1.  能够轻松启动和停止 python 应用程序
2.  流式输出，以便我可以实时显示给用户

### 明显的选择

作为 node 的开箱即用 api 的一部分，他们有一个名为[子进程](https://nodejs.org/api/child_process.html)的东西，这是有据可查的，并且有大量的教程。所以我把这个片段添加到我的应用程序中:

```
const { spawn } = require("child_process");

var pyProcess = spawn("python", "PATHTOFILE.py");

pyProcess.stdout.setEncoding("utf8");
pyProcess.stdout.on("data", data => {
  console.log(data);
});

pyProcess.stdout.on("end", data => {
  console.log("Token " + token + ": closing connection.");
}); 
```

Enter fullscreen mode Exit fullscreen mode

这似乎很管用，直到我意识到代码打印到 stdout 时并没有立即发出“数据”事件，而是被缓冲了。虽然缓冲对于某些用例来说可能更好，但我想要一种类似 IDE 的体验，输出感觉像终端输出。

### 无人维护的选择

虽然我确信有办法说服节点子进程不缓冲输出，但我的研究实际上引导我找到了满足我两个需求的 [pty.js](https://github.com/chjj/pty.js/) 。作为一个额外的奖励，代码不必改变太多:

```
const { spawn } = require("pty.js");

var pyProcess = spawn("python", [scriptPath]);

pyProcess.on("data", data => {
  console.log(data);
});

pyProcess.on("exit", exitCode => {
  console.log("Exiting with code " + exitCode);
}); 
```

Enter fullscreen mode Exit fullscreen mode

然而，当从 macos 跳到 linux 然后再跳回来时，我注意到进程完成时的行为并不一致。更确切地说,“退出”事件似乎没有在 macos 上发出。很快我意识到，在 linux 中，事件也没有得到正确的触发——当 python 代码完成时，它会抛出一个错误，导致“退出”事件被启动。

在本地对 lib 进行了一段时间的修补并检查了它的 github 问题后，我确信这是 pty.js 的问题。我还意识到该项目似乎被放弃了，尽管有相当多的 pull 请求。

### 叉这个！

幸运的是，一个善良的灵魂将这个项目交给了 NodePty 并积极地维护它。有趣的是，我发现了这个分叉，因为 pty.js 上有一个[未决 PR](https://github.com/chjj/pty.js/pull/191) 来声明它未被维护并指向 NodePty。

我想要的一切只是通过切换依赖关系和用:
替换导入来工作

```
const { spawn } = require("node-pty"); 
```

Enter fullscreen mode Exit fullscreen mode

令人惊讶的是，OSS 社区正在开发和维护这么多令人惊奇的工具。这些只是我构建我想要的选项的一小部分，这真是太棒了！