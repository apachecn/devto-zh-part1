# 移除。来自 git 存储库的 DS_Store

> 原文：<https://dev.to/adamkdean/remove-dsstore-from-git-repository-45l1>

以下内容将帮助您去除。来自 git 存储库的 DS_Store。

```
$ find . -name .DS_Store -print0 | xargs -0 git rm --ignore-unmatch
$ echo ".DS_Store" >> .gitignore
$ git add .gitignore
$ git commit -m ".DS_Store removed from repository" 
```

现在你可以放松了。