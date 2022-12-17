# 管道至 fzf

> 原文：<https://dev.to/jrock2004/piping-to-fzf>

那么你在 zshrc 中为使用 FZF 设置了哪些好的别名呢？让我给你一些我有的例子，也许你可以对你的一些别人可能感兴趣的评论。

其中一些是我在网上找到的:

```
alias gcorb='gco --track $(git branch -r | fzf)'
alias gcob='gco $(git branch | fzf)'
alias gmob='git merge $(git branch | fzf)'

fkill() {
  kill -9 $(ps ax | fzf | awk '{ print $1 }')
}

alias apps='dpkg-query --show | fzf' 
```

Enter fullscreen mode Exit fullscreen mode