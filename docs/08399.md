# 在 Bash 中为用户输入添加文件/目录自动完成功能

> 原文：<https://dev.to/aussieguy/add-file-directory-auto-completion-to-user-input-in-bash-703>

在 Bash 脚本中获取用户输入的最常见方式是使用 read 函数，如下所示:

```
#!/bin/bash
echo "Type a word and press enter"
read INPUT
echo "Your word was $INPUT" 
```

但是，当您希望用户输入目录路径时，这不是最好的方法。这是因为按下`tab`会进入一个文字标签，而不是像在终端中那样自动完成目录路径。

要将目录自动完成添加到 bash 脚本中，您所要做的就是将`-e`标志添加到 read 函数中，如下所示:

```
#!/bin/bash
echo "Type a file or directory (use tab to autocomplete path!) and press enter"
read -e INPUT
echo "Your path was $INPUT" 
```