# 当聚焦于 Vim8 中的终端窗口时，自动切换到插入模式

> 原文：<https://dev.to/minodisk/automatically-switch-to-insert-mode-when-focusing-on-terminal-window-in-vim8-9ab>

```
autocmd BufWinEnter,WinEnter * if &buftype == 'terminal' | silent! normal i | endif 
```

Enter fullscreen mode Exit fullscreen mode

根据`:help Terminal-mode`，`startinsert`在终端窗口不起作用。

> 在终端正常模式下，你可以用通常的 Vim 命令移动光标，可视地标记文本，猛拉文本，等等。但是你不能改变缓冲区的内容。启动插入模式的命令，如“I”和“a ”,返回到终端作业模式。窗口将被更新以显示终端的内容。|:startinsert|无效。