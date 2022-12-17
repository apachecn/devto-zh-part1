# 基本 UNIX 命令

> 原文：<https://dev.to/19h47/basic-unix-commands>

最近，我开始学习命令行。我的意思是，我用了`npm install`或`gulp`，但没有更多的。

所以我开始使用命令行，我不得不说我喜欢它。很多。

我想和你分享我学到的东西。

## `cd`

`cd`命令用于改变当前目录。

> *cd* 代表**改变目录**T4】

```
cd path/of/directory 
```

Enter fullscreen mode Exit fullscreen mode

> 要向上移动一个目录，你可以点击`cd ..` [来源](https://stackoverflow.com/a/3267526)

## `ls`

`ls`命令列出了当前目录中除了目录以外的所有文件。

> *ls* 代表**列表段**T4】

```
ls 
```

Enter fullscreen mode Exit fullscreen mode

## `man`

`man`命令显示在线手册页。我们可以指定一个部分作为参数。该部分通常是命令、函数或文件名。

> *man* 代表**手动**。

```
man name_of_command 
```

Enter fullscreen mode Exit fullscreen mode

> 要退出**手动**，可以点击`q`、`:q`、`Q`、`:Q`或`ZZ`。

## `mkdir`

`mkdir`命令用于创建一个新目录。该目录将在当前目录中创建。

> *mkdir* 代表**制作目录**。

```
mkdir name_of_directory 
```

Enter fullscreen mode Exit fullscreen mode

## `rmdir`

`rmdir`命令用于删除一个空目录。

> *rmdir* 代表**删除目录**。

```
rmdir name_of_directory 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过一个`-p`选项。在这种情况下，每个目录参数将被视为单个路径，如果它们为空，将被删除。

```
rmdir -p name/of/directory 
```

Enter fullscreen mode Exit fullscreen mode

## `mv`

`mv`命令用于将文件移动到给定的目录中。

> *mv* 代表**移动**。

```
mv name_of_file name/of/directory/destination/ 
```

Enter fullscreen mode Exit fullscreen mode

## `open`

`open`命令用于打开文件或目录

```
open name_of_file 
```

Enter fullscreen mode Exit fullscreen mode

我知道，这是非常基本的，但你必须从某个地方开始。ðŸ˜…

我创建了一个[要点](https://gist.github.com/19h47/5b9bf949d999a47426df6443dbfb2bad)，在那里我加入了我所学到的东西。

而你，你最喜欢的命令是什么？