# 模糊 grep

> 原文：<https://dev.to/matsuikazuto/fzf--ag-fuzzy-grep-29d>

在我的环境中，[官方 fzf wiki](https://github.com/junegunn/fzf/wiki/Examples) 中描述的 vg()函数效果并不好。
具体来说，打开一个添加了行号的新文件。

我搜了一下，发现[这一期](https://github.com/junegunn/fzf/issues/429)。

```
fag(){
  local line
  line=`ag --nocolor "$1" | fzf` \
    && vim $(cut -d':' -f1 <<< "$line") +$(cut -d':' -f2 <<< "$line")
} 
```

Enter fullscreen mode Exit fullscreen mode

这个功能很好用。这很有帮助。