# 作为字体管理器的 Linuxbrew

> 原文：<https://dev.to/tani/linuxbrew-as-a-font-manager-5af2>

在 macOS 中，我们有一个有用的字体管理器，你可以用一个命令安装各种字体；

```
$ brew tap caskroom/fonts
$ brew install font-inconsolata 
```

Enter fullscreen mode Exit fullscreen mode

而在 Linux 中，我们有一个名为`linuxbrew`的自制分支，它不支持 caskroom。今天，我建议您使用新的解决方案，即 asciian/homebrew-fonts tap。要安装新字体，您只需键入即可；

```
$ brew tap linuxbrew/fonts
$ brew install font-inconsoltata --HEAD 
```

Enter fullscreen mode Exit fullscreen mode

安装后，我们会将字体应用到您的系统中。

```
$ ln -s ~/.lixnubrew/share/fonts ~/.fonts
$ fc-cache -fv # Don't forget this! 
```

Enter fullscreen mode Exit fullscreen mode

我用 Ruby 写了一个带有`parslet`的木桶解析器，它将木桶翻译成公式。我会通过 caskroom/homebrew-fonts 的更新来更新这个库。如果你想让我添加新的字体，那么你可以提交上游 caskroom/家酿字体。我通过上游存储库的更新来跟踪您。请检查一下！谢谢你。

*   [linuxbrew/homebrew-fonts](https://github.com/linuxbrew/homebrew-fonts)
*   [linuxbrew/brew](https://github.com/linuxbrew/brew)