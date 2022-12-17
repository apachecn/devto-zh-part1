# 在 OSX 安装自制软件

> 原文：<https://dev.to/adamkdean/install-homebrew-on-osx-57cj>

要在 OSX 上安装家酿软件，只需在终端上运行以下命令:

```
ruby -e "$(curl -fsSL https://raw.github.com/Homebrew/homebrew/go/install)" 
```

Enter fullscreen mode Exit fullscreen mode

现在测试它的工作情况:

```
brew --version 
```

Enter fullscreen mode Exit fullscreen mode

在使用它安装任何东西之前，请确保快速运行:

```
brew doctor 
```

Enter fullscreen mode Exit fullscreen mode

更多关于自制的信息，请访问[自制维基](https://github.com/Homebrew/homebrew/wiki)。