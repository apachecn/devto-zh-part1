# CDPATH + GOPATH

> 原文：<https://dev.to/leslie/cdpath--gopath-1l66>

# CDPATH + GOPATH

如果你曾经使用过 [Go](http://golang.org) ，我敢肯定你听说过术语 **GOPATH** 。它指的是内部目录结构，Go 工具链用它来查找源代码和包。

如果我有一个名为**天气**的项目，并且我想`cd`进入其中，我必须这样做(假设我的 **$GOPATH** 设置为 **$HOME/go** )。

```
cd $HOME/go/src/github.com/burugux/weather/ 
```

Enter fullscreen mode Exit fullscreen mode

这样做很好，但是如果 **weather** 是一个你经常访问的目录，输入这些内容会变得很无聊，非常重复，还有点烦人。

但是，如果我们可以用 cd 播放天气预报，并且可以直接跳转到那个目录，那不是很酷吗？

### 引入 CDPATH

事实证明，在 **CDPATH** 的帮助下，我们确实可以做到这一点。[学习 bash shell，第三版](http://shop.oreilly.com/product/9780596009656.do)将 CDPATH 定义为

> 该变量的值与**路径**的值一样，是由冒号分隔的目录列表。其目的是增强 cd 内置命令的功能。

默认情况下 **CDPATH** 未设置。为了设置它，以便我们可以更快地跳转到我们的 Go 项目，我们会这样做

```
export CDPATH=$HOME/go/src/github.com/$USER/ 
```

Enter fullscreen mode Exit fullscreen mode

那么上面的命令是做什么的呢？它给 **CDPATH** 变量赋一个值，这个变量就是我们在寻找感兴趣的目录时希望`cd`使用的位置。如果我们输入 **cd 天气**，我们会自动转到

```
$HOME/go/src/github.com/$USER/weather 
```

Enter fullscreen mode Exit fullscreen mode

多酷啊。😃

但是请注意

> 只有当你的 github 用户名和 **$USER** 相同时，上面的方法才有效。如果不是，你所要做的就是用你实际的 github 用户名替换**$用户**。

要使其持久化，请将导出语句添加到您的**中。巴沙尔**或**。zshrc** 。

### 添加更多选项

让我们想象一个场景，在你的 **GOPATH** 中有多个你参与的项目，其中一些不在你的 github 用户名下，这是通常的情况。利用我们在上面学到的知识，是否仍然有可能快速跳转到那些项目，而不需要输入完整的路径？是的。

为此，我们将传递第二个值给由冒号分隔的 **CDPATH** 。例如

```
export CDPATH=$HOME/go/src/github.com/burugux:$HOME/go/src/github.com/ 
```

Enter fullscreen mode Exit fullscreen mode

这样一来，我们就可以做类似于
的事情了

```
cd jessfraz/weather 
```

Enter fullscreen mode Exit fullscreen mode

并自动转到

```
$HOME/go/src/github.com/jessfraz/weather 
```

Enter fullscreen mode Exit fullscreen mode

整洁！😁

如果我们有来自 github 和 gitlab 的项目，我们还能使用 **CDPATH** 吗？又一次是的。我们所要做的就是向`CDPATH`传递第三个值，它看起来就像这样。

```
export CDPATH=$HOME/go/src/github.com/burugux:$HOME/go/src/github.com:$HOME/go/src/gitlab.com/ 
```

Enter fullscreen mode Exit fullscreen mode

现在我可以做

```
cd leslie/cool-project 
```

Enter fullscreen mode Exit fullscreen mode

并自动转到

```
$HOME/go/src/gitlab.com/leslie/cool-project 
```

Enter fullscreen mode Exit fullscreen mode

### 需要注意的一点

如果我有一个目录 **$HOME/weather** ，当我在 **$HOME** 的时候我输入 **cd weather** ，它会把我带到哪里？

```
./weather 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
$HOME/go/src/github.com/burugux/weather 
```

Enter fullscreen mode Exit fullscreen mode

> 不幸地。从 [softparanoma](http://www.softpanorama.org/Scripting/Shellorama/cdpath.shtml) ，如果你在`$CDPATH`中的每个路径上加一个斜杠“/”，你将“cd”到本地目录。如果您没有在每个路径名后面加一个斜杠，您将“cd”到包含匹配子目录的`$CDPATH`列表中的第一个路径名。

要进入本地目录，我必须执行`cd ./weather`,但是从其他地方，我仍然可以执行`cd weather`,然后进入

```
$HOME/go/src/github.com/burugux/weather 
```

Enter fullscreen mode Exit fullscreen mode

我希望你已经学到了一些新的东西，并打算使用`CDPATH`来使你在使用终端时的生活更轻松。

感谢您的阅读！