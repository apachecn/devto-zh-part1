# 用 rbenv 设置 Ruby 开发设置

> 原文：<https://dev.to/prathamesh/setting-up-ruby-development-setup-with-rbenv-33id>

我想在我的机器上安装 ruby 源代码。Github 上的[源](https://github.com/ruby/ruby)有关于如何进行设置的详细说明。

```
git clone github.com/ruby/ruby
cd ruby
autoconf
./configure
make
make install 
```

Enter fullscreen mode Exit fullscreen mode

在 Mac OS X 上工作正常，Ruby 安装于`usr/local/bin`。

我使用 rbenv 来管理 Ruby 版本。在从源代码设置了 Ruby 之后，我想使用 rbenv 来使用它。为什么？这将使开发版本和普通版本之间的切换非常容易。`rbenv shell ruby-dev`，做点什么，做点改动，重新编译，重新测试。`rbenv shell 2.1.2`，又恢复正常。

但是由于源代码 Ruby 安装在`usr/local/bin`中，rbenv 找不到它。

我在 IRC 上问了一个关于#ruby 的问题，后现代指引我去了这个。

它提到了将`--prefix`选项传递给`./configure`命令。我们可以将想要安装 Ruby 的目录名传递给这个选项。

所以我们可以跑

```
./configure --prefix=PATH_TO_INSTALL_RUBY 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，安装`~/.rbnev/versions`中的所有红宝石

当我们执行`rbenv versions`时，它会查找安装在该目录中的所有红宝石并列出它们。

我的`~/.rbnev/versions`目录的内容如下:

```
prathamesh ~/.rbenv/versions 2.0.0
$ ls
2.0.0-p247 2.0.0-p353 2.0.0-p481 2.1.0 2.1.1 2.1.2 rbx-2.2.6

prathamesh ~/.rbenv/versions 2.0.0 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们给 configure 命令提供前缀路径，dev-ruby 将安装在~/中。rbenv/版本

```
./configure --prefix="$HOME/.rbenv/versions/ruby-dev" 
```

Enter fullscreen mode Exit fullscreen mode

在`make`和`make install`之后，dev ruby 被安装在 ruby-dev 目录下的`~/.rbnenv/versions`中。

现在`rbenv versions` output 也有了 ruby-dev。

```
prathamesh ~/.rbenv/versions 2.0.0
$ rbenv versions
* system (set by /Users/prathamesh/.rbenv/version)
  2.0.0-p247
  2.0.0-p353
  2.0.0-p481
  2.1.0
  2.1.1
  2.1.2
  rbx-2.2.6
  ruby-dev

prathamesh ~/.rbenv/versions 2.0.0 
```

Enter fullscreen mode Exit fullscreen mode

由于`ruby-dev`被`rbenv versions`列出，我可以很容易地切换到它:

```
rbenv shell ruby-dev 
```

Enter fullscreen mode Exit fullscreen mode

### 更新

在 Mac OS X Sierra 上，我还必须在配置时传递 openssl 目录的标志。

```
cd ruby
make clean
./configure --prefix="$HOME/.rbenv/versions/ruby-dev" --with-opt-dir="/usr/local/opt/openssl/"
make
make install 
```

Enter fullscreen mode Exit fullscreen mode

这在我的机器上设置了 Ruby dev 版本，并允许我使用 rbenv 来回切换。