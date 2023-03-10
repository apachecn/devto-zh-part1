# “我的 Shell 工作流:使用 bash 历史”

> 原文：<https://dev.to/shriharshmishra/my-shell-workflow-using-bash-history-8k7>

### 双手被绑在外壳上工作:|

虽然我尽可能多地使用我的流浪者盒子来工作，但有时我不得不通过 ssh 连接到服务器，却没有我最喜欢的 z-shell。每次都重复同样的一套命令，真是令人厌烦。一个选择是创建我自己的别名，但我每次登录都必须这样做。我们的系统管理员(奇怪地)确保每个人都可以对一个普通用户做任何编辑工作。

### 寻找出路:)

所以我使用下面的一组命令来确保我不会被这个过程所困扰。

假设我想搜索一个我以前用过的命令。我输入`history | grep <command-snippet>`,当我之前使用这个命令时，它给出了一个编号列表。然后我用这个数字再次运行同一个命令- `!<number>`。见下图——

```
> history | grep vim
2795  vim scratch-pad.txt
2820  vim .git/config
2822  vim .git/config
2845  sudo vim /etc/hosts
2847  sudo vim /etc/hosts

> !2820
> vim .git/config 
```

Enter fullscreen mode Exit fullscreen mode

这可能还是泰德斯。所以另一个选择是不调用`history`命令搜索历史。

### 救世主`Ctrl+R` :D

*   在 bash 终端上，按`Ctrl+R`打开反向搜索对话框。
*   键入您之前执行的命令片段。
*   按`Ctrl+O`执行命令。或者按`Ctrl+Space`打印命令进行编辑后再执行。

```
(reverse-i-search)`install': sudo apt-get install ant #After pressing Ctrl+O
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
.... 

(reverse-i-search)`zsh': sudo apt-get install zsh #After pressing Ctrl+Space
> sudo apt-get install zsh 
```

Enter fullscreen mode Exit fullscreen mode

在 bash 提示符下使用`Ctrl+R`的一个有趣的副作用是，一旦选择的命令完成，历史中的下一个命令就会打印在提示符下，供我们在需要时使用。大多数时候，我重启我的服务器并跟踪日志。所以在我执行重启命令后，使用这个选项会自动返回下一个命令(`tail -f`)。

我希望这也能帮助你。