# 我在 mac 上安装 Ruby 的方式

> 原文：<https://dev.to/toyotarone/the-way-i-installed-ruby-on-my-mac-1lah>

# 先决条件

请在你的 mac 上安装[家酿(官方)](https://docs.brew.sh/)。

# 安装 rbenv 和 ruby-build

首先，我推荐你安装 [rbenv](https://github.com/rbenv/rbenv) 和 [ruby-build](https://github.com/rbenv/ruby-build) 。它们使你能够灵活地控制 Ruby 版本。

```
brew install rbenv ruby-build 
```

Enter fullscreen mode Exit fullscreen mode

虽然听说这种方式倾向于屈服(不要赶最新版本的 ruby)，但是简单。

# 初始化 rbenv

只需执行下面的

```
rbenv init 
```

Enter fullscreen mode Exit fullscreen mode

该命令会将`eval "$(rbenv init -)"`添加到您的`~/.bash_profile`中。
请这样检查

```
less ~/.bash_profile 
```

Enter fullscreen mode Exit fullscreen mode

如果找不到线`export PATH="$HOME/.rbenv/bin:$PATH"`和`eval "$(rbenv init -)"`，请自行添加。

```
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)" 
```

Enter fullscreen mode Exit fullscreen mode

之后，用这个命令重启你的终端或者重新加载你的`.bash_profile`。

```
source ~/.bash_profile 
```

Enter fullscreen mode Exit fullscreen mode

你可以通过这个命令
来检查你是否可以安装`rbenv`

```
rbenv -v 
```

Enter fullscreen mode Exit fullscreen mode

该命令将返回`rbenv`的版本

# 选择 ruby 版本并安装。

好了，现在我们可以从下面得到 Ruby 的版本列表。

```
rbenv install -l 
```

Enter fullscreen mode Exit fullscreen mode

这一次，我假设我们选择了`2.5.0`作为目标。

```
rbenv install 2.5.0 
```

Enter fullscreen mode Exit fullscreen mode

许多命令将被执行。做完后，试试这个。

```
rbenv versions 
```

Enter fullscreen mode Exit fullscreen mode

你可以这样做

```
* system
  2.5.0 
```

Enter fullscreen mode Exit fullscreen mode

这表明您有`Ruby 2.5.0`，但您使用的是预装的 Ruby。
那么，让我们用下面的
来改变目标红宝石

```
rbenv global 2.5.0 
```

Enter fullscreen mode Exit fullscreen mode

# 检查你的 rbenv

现在，你的 mac 已经准备好使用`Ruby 2.5.0`，但是我建议你检查一下你的`rbenv`的状态。

```
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-doctor | bash 
```

Enter fullscreen mode Exit fullscreen mode

这将指示 rbenv 的安装状态。