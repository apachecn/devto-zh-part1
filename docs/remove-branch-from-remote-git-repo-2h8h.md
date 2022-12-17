# 从远程 git repo 中删除分支

> 原文：<https://dev.to/adamkdean/remove-branch-from-remote-git-repo-2h8h>

要从远程 git 存储库中删除一个分支，您必须推送一个空分支。

```
$ git push origin :branch-name
- [deleted]        branch-name 
```

Enter fullscreen mode Exit fullscreen mode

如果您还想将其从本地回购中删除，请删除该分支:

```
$ git checkout master 
Switch to branch 'master'

$ git branch -D branch-name
Deleted branch branch-name (was abcd123) 
```

Enter fullscreen mode Exit fullscreen mode

我们更改分支，因为您不能删除您所在的分支。