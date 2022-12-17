# Git 记录文件的一系列行

> 原文：<https://dev.to/david_j_eddy/git-log-a-range-of-lines-of-a-file-bbn>

今天发现了这个方便的命令，它是一个新的用法，增加了`-L start,end:file/path`。追溯特定器械包线组的变更时非常方便。

`git log -L 50,75:./some/path/file.ext`