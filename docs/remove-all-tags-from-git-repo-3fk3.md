# 从 git repo 中删除所有标签

> 原文：<https://dev.to/adamkdean/remove-all-tags-from-git-repo-3fk3>

我已经开始用分支名称和日期标记构建，但是过了一段时间，这些开始使日志不可读。是时候让阿诺德·施瓦辛格把它们变成电影了。

我在网上找到了这个:

```
for t in `git tag`
do
    git push origin :$t
    git tag -d $t
done 
```

Enter fullscreen mode Exit fullscreen mode

保存到`removetags.sh`，运行它，现在我的生活(和日志)快乐多了。