# iTerm2 中的 ctrl-元空间

> 原文：<https://dev.to/lepovirta/ctrl-meta-space-in-iterm2-1jhn>

在 Emacs 中，有一个方便的命令叫做`mark-sexp`。它从光标位置标记当前 s 表达式的其余部分。默认映射到`C-M-SPC` (ctrl + meta + space)，兼容终端应用。

但是，如果 iTerm2 中有映射到`+Esc`的 option 键，则按 ctrl + option + space 会被解释为 meta + space。要解决这个问题，您需要向会话的键绑定添加一个额外的键绑定。

1.  在 iTerm2 中，打开:首选项>配置文件>要编辑的会话>密钥。
2.  通过按“+”按钮添加新的密钥绑定。
3.  选择“键盘快捷键”栏，然后按 ctrl + option + space。
4.  在“操作”列表中选择“发送十六进制代码”。
5.  在列表下方的字段中键入“0x1b 0x00”。
6.  单击确定并关闭首选项窗口。

`C-M-SPC`现在可以在所有使用已编辑会话的新终端窗口中工作。您还可以通过编辑正在运行的会话，对正在运行的终端进行相同的更改。