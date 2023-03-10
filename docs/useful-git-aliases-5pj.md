# 有用的 git 别名

> 原文：<https://dev.to/adamkdean/useful-git-aliases-5pj>

最近太忙了，这个博客都被忽略了。我计划修复一些设计上的怪癖，并添加一些存档，甚至可能给它一个面貌焕然一新的 biy，这样它就不会那么单调。我不希望发生的事情是，当我的归档出现空白的月份，所以今天是 7 月的最后一天，我将向您展示我的 git 配置中有哪些别名。

```
[alias]
    a = "!git add -A . && git add -u"
    ab = branch -avv
    dt = difftool
    s = status
    b = branch -a
    k = !gitk --all --select-commit=HEAD &
    r = remote -v
    rh = reset --hard
    pr = "!git add . && git add -u && git stash && git pull && git stash pop"
    logp = log -n10 --decorate --graph --pretty=tformat:\"%C(yellow)%h%Creset%C(cyan)%d%C(reset) %s %C(green)(%an, %ar)%C(reset)\" --relative-date
    diffp = diff --ignore-space-at-eol -b -w
    llm = log -1
    unadd = reset --
    ua = reset --
    h = rev-list --max-count=1 HEAD
    list-merge-backups = !git status -su | grep -e'\\.orig$' | cut -f2 -d' '
    delete-merge-backups = !git list-merge-backups | xargs rm
    mergefrom = merge -s recursive -X theirs 
```

Enter fullscreen mode Exit fullscreen mode

嗯，那是一篇简单的博文，但是没有这些我无法使用 git，它们节省了很多时间！