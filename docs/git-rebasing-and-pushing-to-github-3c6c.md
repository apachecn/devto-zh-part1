# Git Rebase 工作流到 Github

> 原文：<https://dev.to/rendegosling/git-rebasing-and-pushing-to-github-3c6c>

我希望分享我在工作流程中使用的一个有用的 git 要点。几年前，我在《米迦勒的要旨》中看到了这个

### **将“特征/我的分支”重置到主**

#### *如果你在分公司单干...*

```
$ git checkout feature/my_branch # make sure you're on the right branch
$ git fetch # update remote refs
$ git rebase origin/master # perform the rebase from master
  # for each conflict, edit file, resolve conflicts, git add -u <file>, git rebase --continue
$ git push -f origin feature/my_branch # overwrite remote branch with newly rebased branch 
```

Enter fullscreen mode Exit fullscreen mode

#### *如果你和别人一起在一个分公司工作...*

不要变基！从事分支工作的作者遵循上面的步骤，然后所有其他贡献者也这样做...

```
$ git checkout feature/my_branch # make sure you're on the right branch
$ git fetch # update remote refs
$ git reset --hard origin/feature/my_branch # discard the state of your branch, move to the newly rebased branch 
```

Enter fullscreen mode Exit fullscreen mode

### **将“特征/我的分支”重置到主文件上，取消所有提交**

```
$ git checkout feature/my_branch
$ git fetch
$ git reset --hard origin/master # move your branch to the current state of master
$ git merge --squash head@{1} # merge all changes from your branch into the index
$ git status # verify changes to be committed
$ git commit # edit all commit messages from previous commits down to a single sane commit message
$ git push -f feature/my_branch # overwrite remote branch with newly rebase branch 
```

Enter fullscreen mode Exit fullscreen mode