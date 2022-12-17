# 节点主流程如何启动？

> 原文：<https://dev.to/captainsafia/how-does-the-node-main-process-start-59nf>

好吧！所以在[我之前的一篇博文](https://dev.to/captainsafia/how-does-processbinding-in-node-work-45a6)中，我试图弄清楚节点主进程是如何初始化的。我最终没有成功。事实证明，我用来作为指导我阅读代码的文档的幻灯片有点过时，并且引用了一些被移动的代码库部分。谢天谢地，Node 的一个维护者(@Fishrock123)在 Twitter 上为我[澄清了这个问题，并指引我找到了正确的文件。所以现在我知道该从哪里开始了！](https://twitter.com/fishrock123/status/951515801240199169)

事实证明， [`src/node_main.cc`](https://github.com/nodejs/node/blob/858b48b692dd04e5134c02f23efac94c4e678329/src/node_main.cc) 是一切的起点。让我们开始吧！

*提示戏剧性的音乐*

如果你熟悉 C/C++，那么这个文件的结构很容易理解。`main`函数是编译器在程序启动时自动执行的函数。这个函数的最后一行让我们知道我们正在将所有的参数传递给`node:Start`函数。

```
return node::Start(argc, argv); 
```

Enter fullscreen mode Exit fullscreen mode

我在头文件和源文件中做了一些搜索，在这里找到了`node:Start`函数[的定义](https://github.com/nodejs/node/blob/8938c4c22d720c33441ce95d801056532b99ec18/src/node.cc#L4547)。考虑到它包含的代码行数，这个函数实际上并不算大，但这是因为它主要调用负责引导节点进程的其他函数。来，让我解释一下我的意思。函数中的前几行如下所示。

```
atexit([] () { uv_tty_reset_mode(); });
PlatformInit();
node::performance::performance_node_start = PERFORMANCE_NOW(); 
```

Enter fullscreen mode Exit fullscreen mode

这一点引起了我的注意。我曾在其他系统级代码中见过这个函数，但对它的作用只有一个模糊的概念。关于出口处理。这一次我决定做更多的研究，并发现它负责计算当前进程退出时该做什么。在这种情况下，当节点进程退出时，会调用`uv_tty_reset_mode`函数。我不知道这个函数是做什么的。虽然到现在为止，我知道它有一个`uv`前缀的事实意味着它在某种程度上与`libuv`(小异步 I/O 库)有关。我在`libuv`的文档中做了一些挖掘，在这里找到了一些关于它的细节[。因此，基本上，当您退出节点进程时，您的 TTY 设置也会被重置。](http://docs.libuv.org/en/v1.x/tty.html#c.uv_tty_reset_mode)

旁注:TTY 代表“电传打字机”,是旧时代的遗产。电传打字机是用来连接计算机的外围设备。[这里](https://media.gettyimages.com/photos/teletypewriter-picture-id128576603)是一张某人使用它的照片。我总是觉得这些科技早期遗留下来的小术语非常有趣。给事物命名是很难的，所以当你可以使用旧的名字时，为什么要为每件事物想新的名字呢？

下一位代码调用`PlatformInit` [函数](https://github.com/nodejs/node/blob/8938c4c22d720c33441ce95d801056532b99ec18/src/node.cc#L4062)。我看到很多关于“文件描述符”和“信号”的评论我现在不打算假装知道这些东西是什么，但我目前正在学习操作系统课程，所以将来我可能会更明智地谈论它们！现在，我要说这个函数主要负责初始化一些平台相关的东西。对我来说够好了！

下一个被调用的大函数是`Init`函数。

```
Init(&argc, const_cast<const char**>(argv), &exec_argc, &exec_argv); 
```

Enter fullscreen mode Exit fullscreen mode

通过阅读 Init [函数](https://github.com/nodejs/node/blob/8938c4c22d720c33441ce95d801056532b99ec18/src/node.cc#L4192)的代码，我发现它负责注册内置模块、将开发人员在命令行提供的 V8 配置标志传递给 V8，以及配置其他一些环境变量。这里引起我注意的一个函数调用是这个。

```
node::RegisterBuiltinModules(); 
```

Enter fullscreen mode Exit fullscreen mode

我认为更详细地弄清楚这里发生了什么会很有趣，但我可能会在以后再做。我不想超出我的能力范围。

最后，这个函数中的最后几行代码负责做大量与 V8 相关的工作。

```
V8::Initialize();
node::performance::performance_v8_start = PERFORMANCE_NOW();
v8_initialized = true;
const int exit_code =
  Start(uv_default_loop(), argc, argv, exec_argc, exec_argv);
if (trace_enabled) {
  v8_platform.StopTracingAgent();
}
v8_initialized = false;
V8::Dispose();

// uv_run cannot be called from the time before the beforeExit callback
// runs until the program exits unless the event loop has any referenced
// handles after beforeExit terminates. This prevents unrefed timers
// that happen to terminate during shutdown from being run unsafely.
// Since uv_run cannot be called, uv_async handles held by the platform
// will never be fully cleaned up.
v8_platform.Dispose(); 
```

Enter fullscreen mode Exit fullscreen mode

看起来 V8 被初始化了，然后通过事件循环调用了`Start`函数的多态定义。然后，V8 中的跟踪代理会发生一些有趣的事情。

边注:如果你试图谷歌“跟踪代理”来了解更多关于跟踪代理你会得到一个完全意想不到的结果。另一条新闻是，你显然可以从网上雇佣一支国际搜捕队。科技不是很奇妙吗？

我做了一些关于追踪代理的研究，发现[这个](https://github.com/v8/v8/wiki/Tracing-V8)相当有用的维基文档。因此，跟踪似乎是 V8 中一个相当新的特性。本质上，这段代码负责配置 V8 的 tracer 使用的特殊日志记录所需的自定义代码。有道理。

接下来的两行对我来说有点奇怪。

```
v8_initialized = false;
V8::Dispose(); 
```

Enter fullscreen mode Exit fullscreen mode

我们刚刚初始化了 V8 解释器，但是现在我们将它设置为已初始化并处理它。说什么？为了弄清楚这里发生了什么，我决定进入代码文件的历史，看看这些变化是什么时候被引入代码库的，并找到了大约 9 年前的[这个提交](https://github.com/nodejs/node/commit/27b268b8c13d4ca27a0755cc02446fb78886a3bf)。从提交来看，`V8:Dispose`行实际上是在某个特定测试中导致一些错误后被添加回代码库的。这仍然不能让我很好地理解它为什么被释放，所以我决定弄清楚`V8:Dispose`函数实际上做什么。

我发现[一些文档](https://v8.paulfryzel.com/docs/master/classv8_1_1_v8.html#af761970f495ed98e940808fd4fa5caff)解释说`V8:Dispose`负责释放任何被 V8 使用的资源。我开始意识到这里发生的事情是节点进程实际上在`Start`函数中被清理了。所以多态的`Start`函数在这里被调用。

```
const int exit_code =
  Start(uv_default_loop(), argc, argv, exec_argc, exec_argv); 
```

Enter fullscreen mode Exit fullscreen mode

运行，直到从节点退出。此时，您需要完成 V8 引擎的所有清理工作，并暂停 V8 使用的跟踪代理。这是有道理的。为了验证这个假设，我必须进入并读取被调用的`Start`函数的代码。

像往常一样，我让我的代码读起来比开始时更加混乱和无知！从这里我可以去很多地方。尽管只有大约 50 行代码(带注释和换行符)，但这个函数给了我很多想用节点解释器探索的东西的想法。

*   内置模块是如何加载的？
*   `PlatformInit`到底在做什么？这些文件描述符和信号是怎么回事？
*   `Start`函数的所有多态变体是做什么的？

我会试着在其他博客文章中把这些分开。

整个过程非常有趣。这个代码库的 C/C++部分相当粗糙。尤其是对我来说，我只在学术背景下使用过 C/C++，所以阅读这些“行业优势”代码相当有趣。

这也表明，每个维护 Node 的人都在做一些真正严谨和令人敬畏的技术工作，我们都应该热爱和赞扬他们！