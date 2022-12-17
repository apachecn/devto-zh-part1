# 在 Deis 中扩展 Dockerfile 应用程序

> 原文：<https://dev.to/adamkdean/scale-a-dockerfile-app-in-deis-2p1>

Deis 允许您以两种不同的方式运行应用程序:使用 Procfile 和使用 Dockerfile。使用 Procfile 时，您可以定义将要运行的进程类型，例如:

```
web: node index.js 
```

Enter fullscreen mode Exit fullscreen mode

要对此进行扩展，比方说扩展到两个实例，简单如下:

```
$ deis ps:scale web=2 
```

Enter fullscreen mode Exit fullscreen mode

然而，当使用 Dockerfile 方法时，这不起作用。为此，我们可以从 ps:list 中看到实例类型实际上是 cmd。

```
$ deis ps:list
--- cmd:
cmd.1 up (v2) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们使用一个 Procfile，这将表示 web.1 up (v2)等。所以要缩放 Dockerfile，我们需要使用 cmd 而不是 web。

```
$ deis ps:scale cmd=2 
```

Enter fullscreen mode Exit fullscreen mode

然后，一旦有了咖啡，它就会扩展你的应用程序。