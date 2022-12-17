# 使用单个前缀键提高 tmux 的生产率

> 原文：<https://dev.to/ggarnier/increasing-productivity-in-tmux-with-a-single-prefix-key-cib>

最初发布于[古伊列梅·卡尼尔的博客](https://blog.guilhermegarnier.com/2017/12/increasing-productivity-in-tmux-with-a-single-prefix-key/)。

[Tmux](https://github.com/tmux/tmux) 是提高终端工作效率的绝佳工具。开始使用 tmux 时，人们首先配置的事情之一是更改前缀密钥。默认值为`control+b`，单手按下不太舒服。因为您最终会频繁地按下它，所以对于每个 tmux 命令，最常用的配置是将其更改为`control+a`。

这要好得多，但是在输入任何 tmux 命令之前，您仍然需要同时按下两个键。在使用这种配置一段时间后，我决定将其改为单键，以使它更加简单。

我想过把前缀改成`caps lock`。除了很少使用之外，它的定位非常好。但是在 tmux 中不能设置`caps lock`为前缀。另一个解决方案是将`caps lock`键映射到其他东西。在 OSX，你可以设置成另一个修饰键，比如`control`、`shift`或`esc`:转到`System Preferences` = >、`Keyboard`=>=`Modifier keys`。首先，我尝试将其映射到`esc`，并将`esc`设置为 tmux 前缀。它工作了，但是这个设置带来了另一个问题:作为一个 vim 用户，我经常使用`esc`键(在 vim 模式之间切换)，所以现在我必须键入`esc` / `caps lock`两次才能将`esc`键发送给 vim。还可以，但不理想。

然后我尝试了另一个解决方案:我安装了 [Karabiner-Elements](https://github.com/tekezo/Karabiner-Elements) ，这是一个 Mac 应用程序，可以让你完全定制你的键盘。于是我把`caps lock`键映射到`Home`(Mac 键盘中没有)，把 tmux 前缀键改成`Home` :

```
set -g prefix Home
unbind C-b
bind-key Home send-prefix 
```

Enter fullscreen mode Exit fullscreen mode

现在我有了一个很好的配置:我使用一个单键(`caps lock`)作为前缀，并且没有丢失任何键的功能。