# 从命令行做简单的笔记

> 原文：<https://dev.to/ricardomol/note-taking-from-the-command-line-156>

这是一个可以从命令行调用的简单函数，就像它是一个常规命令一样。这是记笔记并将其存储在文本文件中的快捷方式。

只需将以下几行添加到您的`~/.bashrc` :

```
# My function to take quick notes on useful commands
notes() {
    echo $1 >> $HOME/notes.md
} 
```

Enter fullscreen mode Exit fullscreen mode

可以这样用:

```
$ notes "my_command -which -I -want -to remember" 
```

Enter fullscreen mode Exit fullscreen mode

你可以这样读你的笔记:

```
$ more ~/notes.md 
```

Enter fullscreen mode Exit fullscreen mode