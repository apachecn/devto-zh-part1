# 通过 Go 开始编写应用程序

> 原文：<https://dev.to/hatajoe/getting-started-to-writing-an-app-by-go-dg2>

在编写 Go 代码之前，我们需要考虑一些事情。这是关于 GOPATH 和供应商包版本管理的。

默认情况下，GOPATH 具有很大的包依赖范围。这导致构建器、编辑器和其他相关工具性能下降。此外，很难管理包版本。 *go get* 获取包到每个包的相同上下文中。

但是我们可以通过根据上下文改变 GOPATH 来解决这个问题。有一点点不同的方法的独立应用程序和可重用的软件包。

我介绍一下如何入门。

# 安装目录环境

在看这个之前，有一篇我之前写的文章，推荐你先看那个。

[为 2017 年末的 Go 准备开发环境](https://dev.to/hatajoe/preparing-the-development-environment-for-go-in-late-2017-a73)

direnv 是一个为 shell 提供环境切换器的工具。

[direnv/direnv](https://github.com/direnv/direnv)

这样，您可以使用任意目录作为 GOPATH。通过这种方式，您可以获得一个最小范围的开发环境。

```
% echo $GOPATH
/Users/hatajoe
% cd work
% mkdir app
% cd app
% direnv edit .
use go go1.9.2
layout go 
```

Enter fullscreen mode Exit fullscreen mode

这样，当移动到 */app* 目录时，GOPATH 就变成了 */Users/hatajoe/work/app* 。

# 安装 dep

dep 是一个用于 Go 依赖管理的工具。

[golang/dep](https://github.com/golang/dep)

通过使用 *dep* ，我们可以随时获取相同的修订包。

# 独立应用

独立的定义就是是否被别人引用。如果你做一个独立的应用程序，这样做。

```
% pwd
/Users/hatajoe/work
% mkdir app
% cd app
% direnv edit .
use go go1.9.2
layout go
% git init
% git add .
% git commit -m “initial commit”
% mkdir -p src/app
% cd src/app
% dep init
% ls
Gopkg.lock Gopkg.toml vendor 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！我们准备开始编码了。

# 可重复使用的包装

如果你做一个可重复使用的包，像这样做。

```
% pwd
/Users/hatajoe/work
% mkdir my-package
% cd my-package
% direnv edit .
use go go1.9.2
layout go
% mkdir src/github.com/hatajoe/my-package
% cd src/github.com/hatajoe/my-package
% dep init
% ls
Gopkg.lock Gopkg.toml vendor
% git init
% git add .
% git commit -m “initial commit” 
```

Enter fullscreen mode Exit fullscreen mode

你知道，它与独立的应用程序有一点不同。

* * *

感谢您的阅读。欢迎反馈。如果您有任何想法和改进，请随时回复我！