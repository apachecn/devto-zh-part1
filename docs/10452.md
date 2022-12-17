# Bundler 的本地宝石

> 原文：<https://dev.to/thelonelyghost/local-gems-with-bundler>

测试一个未发行版本的宝石？想要开发两个基于彼此的未发布项目，并且不必担心以下问题？

```
gem 'some_gem', path: '../my-dev-snapshot' 
```

Enter fullscreen mode Exit fullscreen mode

让`Gemfile`保持原样将会破坏你的项目历史，所以我们希望当 gem 发布时`Gemfile`的版本保持原样，将那个版本保存在我们的“git 内存”中。请看下面:

```
bundle config local.some_gem "$(realpath ../my-dev-snapshot)" 
```

Enter fullscreen mode Exit fullscreen mode

这将允许您的`Gemfile`保持原样，没有`path: '../foo'`的攻击，因此其他人可以设置他们自己的路径到 gem 源目录。

## 说明:

gem，在本例中是`some_gem`，必须指向 git 存储库。在这种情况下，它需要:

```
gem 'some_gem', github: 'foo/bar', branch: 'master' 
```

Enter fullscreen mode Exit fullscreen mode

这将使我们不仅可以优化网络流量，这样我们就不会一直调用 git 存储库，还可以将它指向本地工作副本。

不过，还有一点需要注意。给定的分支——在本例中为`master`——必须与之前用`bundle config`指定的路径上的当前工作副本的分支相匹配。