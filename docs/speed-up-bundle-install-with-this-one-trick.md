# 用这一招加速软件包安装

> 原文：<https://dev.to/philnash/speed-up-bundle-install-with-this-one-trick>

你知道吗 [bundler](https://bundler.io) 可以并行下载和安装 gems？

没错，使用`bundle install`的`--jobs`选项意味着你可以设置可以同时下载和安装的 gem 的数量。

作为奖励，你甚至不用每次都记得使用`--jobs`选项。您可以使用以下命令在全局捆绑器配置中进行设置:

```
$ bundle config --global jobs 4 
```

Enter fullscreen mode Exit fullscreen mode

## 真的有帮助吗？

我最近发现了所有这些，尽管这从 bundler 版本 1.4.0 起就有了，我想测试它实际上有多有用。并行下载和安装 gems 听起来很有趣，但是我需要用一些东西来测试它以确保万无一失。

我去寻找一个大的 Rails 项目来尝试这个。我偶然发现了 [GitLab 社区版应用](https://gitlab.com/gitlab-org/gitlab-ce/)并克隆了这个项目。我不需要实际运行项目，我只是测试 gems 的安装时间。

我在寻找一个大型应用程序。`rake stats`告诉我它包含 172 个控制器，208 个模型，86019 行代码。在 gem 文件中有 197 个 gem 依赖项，总共有 369 个 gem。我也检查了[话语](https://github.com/discourse/discourse)和[散居](https://github.com/diaspora/diaspora)，但是 GitLab 肯定有最多的宝石，所以它是测试我的理论的完美选择。

对于 n 等于 1 和 4 的情况，我分别运行了 5 次`time bundle install --path=./gems --quiet --force --jobs=n`。每个结果的中间值是:

```
time bundle install --path=./gems --quiet --force --jobs=1
real  4m39.836s
user  1m59.692s
sys 0m50.291s 
```

Enter fullscreen mode Exit fullscreen mode

```
time bundle install --path=./gems --quiet --force --jobs=4
real  2m55.857s
user  2m0.005s
sys 0m47.897s 
```

Enter fullscreen mode Exit fullscreen mode

这些测试是在配备 2.5 GHz 英特尔酷睿 i7 和 16GB 内存的 MacBook Pro 上运行的。

从这些结果我们可以看出，使用 4 个工人并行安装 GitLab 的 gems 要快 1.6 倍。

您应该用自己的设置运行自己的测试，看看这是否真的会节省您安装 gems 的时间。已经有一些关于 T2 最佳工作数量的研究。我当然已经将未来的默认作业数设置为 4。

### 这个为什么不默认？

您可能想知道为什么 Bundler 不默认打开并行安装。快速浏览一下[源代码](https://github.com/bundler/bundler/blob/7f1411cdb3279c25e8e8f2a8e3c1f8acf3dbe8f2/lib/bundler/installer.rb#L160-L163)中的注释，就会发现这一点。

```
# the order that the resolver provides is significant, since
# dependencies might affect the installation of a gem.
# that said, it's a rare situation (other than rake), and parallel
# installation is SO MUCH FASTER. so we let people opt in. 
```

Enter fullscreen mode Exit fullscreen mode

## 阅读文档

作为最后一点，我想指出的是，阅读文档，即使是对于一个你可能每天都在使用的项目，也可以发现一些你不知道可以使用的有趣的选项。Bundler 本身充满了其他有用的命令和选项，请查看 [`bundle pristine`](https://bundler.io/v1.15/man/bundle-pristine.1.html) 、 [`bundle gem`](https://bundler.io/v1.15/man/bundle-gem.1.html) 和 [`bundle outdated`](https://bundler.io/v1.15/man/bundle-outdated.1.html) 了解一些可用的想法。

至少如果你不知道`bundle install`的`--jobs`选项，你现在可以出去更快地安装你的宝石。

* * *

*[用这一招加速捆绑安装](https://philna.sh/blog/2017/06/12/speed-up-bundle-install-with-this-one-trick/)原载于[philna . sh](https://philna.sh)2017 . 6 . 12。*