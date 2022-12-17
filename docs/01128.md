# 节点模块深入研究:子进程

> 原文：<https://dev.to/captainsafia/node-module-deep-dive-childprocess-3ci2>

嗨，朋友们！

没错！我带着另一个节点模块回来了。正如我在上一篇文章中提到的，我希望在本月剩下的时间里，发表一些关于 Node 代码库的带注释的代码。我希望在周一、周三和周五发布它们，我为自己遵守了这个承诺感到非常自豪。所以，事不宜迟…

是时候阅读(并注释)一些代码了！在这篇文章中，我首先问自己一个非常基本的问题。使用`child_process.exec`执行命令时会发生什么？对于那些可能不熟悉的人来说，`child_process.exec`是一个让您能够从 Node 执行 shell 命令的函数。你可以这样做。

```
> const { exec } = require('child_process');
undefined
> exec('echo "Hello there!"', (error, stdout, stderr) => {
... if (error) console.log(error);
... console.log(`${stdout}`);
... console.log(`${stderr}`);
... });
> Hello there! 
```

Enter fullscreen mode Exit fullscreen mode

很整洁，是吧？我也这么认为我在构建 [giddy](https://github.com/captainsafia/giddy) 时经常使用这个命令，这是一个小的节点 CLI，为 git 增加了一些有用的特性。

和往常一样，我在 GitHub 上找到 Node.js repo 并导航到 child_process 的[源文件。在前几篇文章中，我通过检查模块的导出开始了我的代码阅读。在这种情况下，我非常清楚应该寻找什么，所以我直接进入了模块上的`exec`命令的定义。](https://github.com/nodejs/node/blob/0a842633861cf20527ce93e6bf58b4ece1b168be/lib/child_process.js) 

```
exports.exec = function(command /*, options, callback*/) {
  var opts = normalizeExecArgs.apply(null, arguments);
  return exports.execFile(opts.file,
                          opts.options,
                          opts.callback);
}; 
```

Enter fullscreen mode Exit fullscreen mode

我认为非常有趣的是，尽管`exec`命令接受三个参数(执行的`command`、使用的`options`和调用的`callback`)，但它被设置为只接受一个参数。似乎为了提取这三个参数，在`arguments`对象上调用了`normalizeExecArgs`函数。然后，`normalizeExecArgs`将传递给`arguments`对象的每个字段提取到一个带有适当标签的对象中。

```
function normalizeExecArgs(command, options, callback) {
  if (typeof options === 'function') {
    callback = options;
    options = undefined;
  }

  // Make a shallow copy so we don't clobber the user's options object.
  options = Object.assign({}, options);
  options.shell = typeof options.shell === 'string' ? options.shell : true;

  return {
    file: command,
    options: options,
    callback: callback
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我觉得这样做很奇怪。在代码库的其他部分，这种类型的检查——用于正确匹配函数是用命令和回调调用但没有选项，还是用命令和选项但没有回调等等——通常在函数体内完成。在这里，它似乎已被委托给一个外部实用功能。这个函数(`normalizeExecArgs`)被调用两次，一次在`exec`中，一次在`exec`同步中，这样处理逻辑可能已经在那里被提取，以保持代码干燥。在任何情况下，当该说的都说了，该做的都做了，现在看起来我们已经得到了一个变量`opts`，它包含一个带有我们想要执行的命令的对象，我们想要用来执行它的选项，以及要调用的回调。

`exec`函数将这些选项传递给`execFile`函数。这是一个庞大的 193 行代码！没关系。我是一个勇敢的女人，我已经读了七遍这些代码，所以我肯定能处理好。你准备好了吗？好吧，我们走。

`execFile`命令的前几行似乎在做一些基本的选项设置和*更多的* `arguments`解析。在这一点上，我有点困惑，为什么位置参数需要再次传递，因为它们已经在`exec`函数中解析过了。这是不寻常的，但我不会让它让我夜不能寐，我们继续前进…

所以在这一点上，我们有—

哦等等！停下来。我刚刚意识到为什么在`execFile`中有一套额外的解析逻辑。虽然`execFile`只在`child_process`模块内部被`exec`函数调用，但它是一个导出函数，开发者可能会调用它。因此，该函数还需要解析开发人员提供的参数。我对涉及`exec`调用`execFile`的思路如此混乱，以至于忘记了`execFile`是公共 API 的一部分。好吧，我说到哪了？

至此，我们已经有了一个 options 对象和一个要调用的回调。接下来的几行验证和 santize 开发人员提供的选项。

```
// Validate the timeout, if present.
validateTimeout(options.timeout);

// Validate maxBuffer, if present.
validateMaxBuffer(options.maxBuffer);

options.killSignal = sanitizeKillSignal(options.killSignal); 
```

Enter fullscreen mode Exit fullscreen mode

下一行用给定的参数和自变量调用`spawn`。

```
var child = spawn(file, args, {
  cwd: options.cwd,
  env: options.env,
  gid: options.gid,
  uid: options.uid,
  shell: options.shell,
  windowsHide: !!options.windowsHide,
  windowsVerbatimArguments: !!options.windowsVerbatimArguments
}); 
```

Enter fullscreen mode Exit fullscreen mode

`spawn`是一个快速的小函数，它创建一个新的 ChildProcess 对象，并使用传递给它的参数调用它的`spawn`函数。

旁注:也许我会在某个时候对 ChildProcess 对象进行代码读取。它现在不在我的通读清单上，但是如果你有兴趣在 Twitter 上看到关于它的帖子，请告诉我。

```
var spawn = exports.spawn = function(/*file, args, options*/) {
  var opts = normalizeSpawnArguments.apply(null, arguments);
  var options = opts.options;
  var child = new ChildProcess();

  debug('spawn', opts.args, options);

  child.spawn({
    file: opts.file,
    args: opts.args,
    cwd: options.cwd,
    windowsHide: !!options.windowsHide,
    windowsVerbatimArguments: !!options.windowsVerbatimArguments,
    detached: !!options.detached,
    envPairs: opts.envPairs,
    stdio: options.stdio,
    uid: options.uid,
    gid: options.gid
  });

  return child;
}; 
```

Enter fullscreen mode Exit fullscreen mode

一旦创建了这个 ChildProcess 对象，`execFile`函数体的其余部分主要负责在新的 ChildProcess 对象上配置事件处理程序。例如，将一个退出处理程序附加到监听退出事件的子进程，并调用作为参数传递给`execFile`函数的回调函数。它还附加了一个错误处理程序，根据开发人员在选项参数中提供的编码对`stderr`进行正确编码。

```
child.addListener('close', exithandler);
child.addListener('error', errorhandler); 
```

Enter fullscreen mode Exit fullscreen mode

总而言之，`child_process`模块中的`exec`函数是围绕`execFile`函数的包装器，后者又扩展了`child_process`模块中的`spawn`函数所做的一些工作，后者依赖于在`ChildProcess`对象中实现的`spawn`逻辑。切洋葱没有我想象的那么痛。

如果您对上述内容有任何问题或意见，请随时在 Twitter 上联系我。