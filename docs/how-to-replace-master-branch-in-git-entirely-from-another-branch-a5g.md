# 如何从另一个分支完全替换 git 中的 master 分支？

> 原文：<https://dev.to/adamkdean/how-to-replace-master-branch-in-git-entirely-from-another-branch-a5g>

如果你想通过合并一个分支来完全取代另一个分支，下面的技巧将会帮助你，就像它帮助我一样:

```
git checkout yourbranch  
git merge -s ours master  
git checkout master  
git merge yourbranch 
```

Enter fullscreen mode Exit fullscreen mode