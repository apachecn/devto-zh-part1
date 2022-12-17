# 用 PowerShell 删除 Git 中的远程修剪分支

> 原文：<https://dev.to/aggieben/deleting-remote-pruned-branches-in-git-with-powershell-7d5>

很容易得到一个大量的分支集合，这些分支不再位于远程位置，这使得 Git 的 gc 无法进行它应该进行的清理。让我们摆脱他们。

```
git branch -vv | sls gone `
  |% { $_.ToString().Trim() -split '\s+' | select -first 1 } `
  |% { git branch -D $_ } 
```