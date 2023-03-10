# Git 提交之间删除的文件数

> 原文：<https://dev.to/adamkdean/count-number-of-files-deleted-between-git-commits-5ai1>

要计算两次 Git 提交之间删除的文件数量，请使用以下命令:

```
$ git log --diff-filter=D --summary 45b0487..HEAD | grep 'delete mode' | wc -l 
```

请确保将`45b0487`更改为您想要计数的提交，或者完全删除`45b0487..HEAD`以查看整个历史。

我们使用`grep`来统计删除文件的数量。

```
$ git log --diff-filter=D --summary | grep 'delete mode'

delete mode 100644 test (3).txt
delete mode 100644 test (4).txt
delete mode 100644 test (2).txt 
```

然后，我们使用`wc -l`进行行数计算:

```
$ git log --diff-filter=D --summary | grep 'delete mode' | wc -l
      3 
```

有时你从`wc -l`得到空白，你可以用`tr`来修剪它:

```
$ git log --diff-filter=D --summary | grep 'delete mode' | wc -l | tr -d ' '

3 
```

对于较大的存储库，这可能需要一些时间。