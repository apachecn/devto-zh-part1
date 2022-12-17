# 您的 UNIX 管道命令中有哪些节省了您大量的编码/时间？

> 原文：<https://dev.to/djviolin/what-are-your-unix-pipeline-commands-that-saved-you-from-lot-of-codingtime-7ok>

这个问题是我昨天想到的，当时我想在我的硬盘上释放一些空间，当谈到要删除什么(感到痛苦)时，我想知道没有很多右键单击哪个文件夹是最胖的。作为一个优秀的 Windows 用户，我安装了 Total Commander，因为一些随机的谷歌搜索结果告诉我这样做。

然后我意识到，见鬼，我在我的 PC (MSYS2)上有一个完整的 UNIX 环境，所以可能有一个非常简单的命令行命令来实现这一点。猜猜看，它有::)

```
$ du -sh * | sort -h 
```

Enter fullscreen mode Exit fullscreen mode

生活帮，把这个放到你的`.bashrc`文件里:

```
# Within the current directory, list all folders and files
# and print their size, then sort it from smallest to largest:
alias du='du -sh * | sort -h' 
```

Enter fullscreen mode Exit fullscreen mode

您的 UNIX 管道命令是什么，在那里您可以结合一些程序的标准输出，并创建一些邪恶的简单的时间节省？