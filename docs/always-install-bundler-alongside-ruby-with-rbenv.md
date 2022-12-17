# 总是在 Ruby 旁边安装 Bundler 和 rbenv

> 原文：<https://dev.to/philnash/always-install-bundler-alongside-ruby-with-rbenv>

对于使用 [rbenv](https://github.com/rbenv/rbenv) 和 [ruby-build](https://github.com/rbenv/ruby-build) 来管理安装和切换 ruby 版本的人，这里有一个快速提示。

当安装一个新版本的 Ruby 时，我想不出在什么情况下我不想安装 [Bundler](http://bundler.io/) 。好消息是，只要你安装了新版本的 Ruby，你就可以让 rbenv 安装 Bundler，或者任何你喜欢的 gem。你所需要的是[默认宝石](https://github.com/rbenv/rbenv-default-gems)插件。

## 默认宝石

如果你像我一样用自制软件安装了 rbenv，那么你也可以用自制软件安装 default-gems。

```
$ brew install rbenv-default-gems 
```

Enter fullscreen mode Exit fullscreen mode

然后你需要列出你想要安装的 gems。首先在您的`$(rbenv root)`目录中创建一个名为`default-gems`的文件。我的矿位于`~/.rbenv/default-gems`。添加您想要安装的 gems 的名称，每行一个。您可以包含一个可选版本或预发布版本的选项`--pre`。对于 Bundler，您需要最新的版本，所以`default-gems`文件应该是这样的:

```
bundler 
```

Enter fullscreen mode Exit fullscreen mode

现在当你安装一个新版本的 Ruby 时，Bundler 也会被安装。

[![A terminal window shows the result of running  raw `rbenv install 2.4.0` endraw . Not only is Ruby 2.4.0 installed, but so is the latest version of Bundler.](img/b66ae2f8e5646ed5efd0d02e09f12e2b.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NlDUqsy2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://philna.img/install_ruby_bundler.png)

你也可以添加其他你经常使用的宝石。例如，如果你是[撬](http://pryrepl.org/)的粉丝，那么在`default-gems`中为它添加一行，你将永远不必记得自己安装它。

## rbenv 插件

你可以查看许多 rbenv 插件，但是 default-gems 对我来说是必须的。

有没有什么工具或插件可以让你的 Ruby 开发变得更容易？我很想听听他们的故事！请[在 Twitter 上与我分享你的建议](https://twitter.com)。

* * *

*[总是用 rbenv 把 Bundler 安装在 Ruby 旁边](https://philna.sh/blog/2017/03/22/always-install-bundler-alongside-ruby-with-rbenv/)原载于[philna . sh](https://philna.sh/)2017 年 3 月 22 日。*