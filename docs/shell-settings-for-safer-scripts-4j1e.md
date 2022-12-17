# 更安全脚本的外壳设置

> 原文：<https://dev.to/adamkdean/shell-settings-for-safer-scripts-4j1e>

我正在通读 Progrium 的一个脚本，这时我在一个脚本的开头看到了`set -eo pipefail`。之前没见过，我决定谷歌一下。这是那件事的结果。

您可以使用`set`来操作 shell 变量和函数。其中一些可以帮助您编写更安全的脚本。

```
set -e 
```

Enter fullscreen mode Exit fullscreen mode

如果任何命令失败，`set -e`将使整个脚本失败，而不仅仅是跳到下一行。如果你想让一行失败，那么你可以在它的末尾弹出`|| true`。

```
set -u 
```

Enter fullscreen mode Exit fullscreen mode

这将把未设置的变量视为错误，并立即退出脚本。

```
set -o pipefail 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，如果失败，只有管道命令列表中的最后一个命令返回失败代码。通过使用`set -o pipefile`，如果任何命令失败，该行将失败。与`set -e`一起使用意味着如果管道命令中的任何命令失败，脚本将失败。

现在回到我的阅读...