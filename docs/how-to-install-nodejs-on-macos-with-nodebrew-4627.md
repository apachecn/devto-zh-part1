# 如何用 nodebrew 在 macOS 上安装 node.js？

> 原文：<https://dev.to/toyotarone/how-to-install-nodejs-on-macos-with-nodebrew-4627>

# 安装自制软件

见下图:
[家酿(官方)](https://brew.sh/)

# 安装 nodebrew

在你安装了 Homebrew 之后，你应该执行下面的

```
brew install nodebrew 
```

Enter fullscreen mode Exit fullscreen mode

如果你想检查你是否能得到它，请键入这个命令。

```
nodebrew -v 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到 nodebrew 的版本。

```
nodebrew 0.9.8

Usage:
    nodebrew help                         Show this message
    nodebrew install <version>            Download and install <version> (compile from source)
    nodebrew install-binary <version>     Download and install <version> (binary file)
    nodebrew uninstall <version>          Uninstall <version>
    nodebrew use <version>                Use <version>
    nodebrew list                         List installed versions
    nodebrew ls                           Alias for `list`
    nodebrew ls-remote                    List remote versions
    nodebrew ls-all                       List remote and installed versions
    nodebrew alias <key> <value>          Set alias
    nodebrew unalias <key>                Remove alias
    nodebrew clean <version> | all        Remove source file
    nodebrew selfupdate                   Update nodebrew
    nodebrew migrate-package <version>    Install global NPM packages contained in <version> to current version
    nodebrew exec <version> -- <command>  Execute <command> using specified <version>

Example:
    # install from binary
    nodebrew install-binary v0.10.22

    # use a specific version number
    nodebrew use v0.10.22

    # io.js
    nodebrew install-binary io@v1.0.0
    nodebrew use io@v1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

在你安装了 nodebrew 之后，我建议你将 nodebrew 的路径添加到你的`.bash_profile`中。

```
echo 'export PATH=$PATH:$HOME/.nodebrew/current/bin' >> ~/.bash_profile 
```

Enter fullscreen mode Exit fullscreen mode

# 安装 node.js

在安装 node.js 之前，请在官网上查看 node.js 的最新版本。
[node.js 官网](https://nodejs.org/en/)
你可能会找到 node.js 的 2 个以上版本
我推荐你安装 LTS(长期支持)版本。

如果您决定了版本，请在下面键入，您可以看到您可以安装的所有版本。

```
nodebrew ls-remote 
```

Enter fullscreen mode Exit fullscreen mode

你能找到你想要的版本吗？好的。让我们安装它。

```
nodebrew install-binary <THE-VERSION-YOU-WANT> 
```

Enter fullscreen mode Exit fullscreen mode

请将替换为您要安装的版本。

如果出现如下错误，

```
Warning: Failed to create the file 
Warning: /Users/<YOUR-USER-NAME>/.nodebrew/src/v8.9.3/node-v8.9.3-darwin-x64.tar.gz: No 
Warning: such file or directory 
```

Enter fullscreen mode Exit fullscreen mode

使用该命令创建所需的文件夹。

```
mkdir -p ~/.nodebrew/src 
```

Enter fullscreen mode Exit fullscreen mode

并重试`nodebrew install-binary <THE-VERSION-YOU-WANT>`

# 启动 node.js

在启动 node.js 之前，请检查 nodebrew。

```
nodebrew ls 
```

Enter fullscreen mode Exit fullscreen mode

可以得到如下:

```
v8.9.3

current: none 
```

Enter fullscreen mode Exit fullscreen mode

这是您拥有的 node.js 版本和当前 node.js 版本的列表。
行`current: none`表示你的系统还没有设置 node.js.
所以，输入这个

```
nodebrew use <THE-VERSION-YOU-WANT> 
```

Enter fullscreen mode Exit fullscreen mode

之后，请执行此

```
node -v 
```

Enter fullscreen mode Exit fullscreen mode

如果您可以适当地设置 node.js，您可以获得如下所示的 node.js 版本的返回结果。

```
v8.9.3 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已！现在您可以使用 node.js 构建令人惊叹的项目了！