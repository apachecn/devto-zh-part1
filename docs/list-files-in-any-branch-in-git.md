# 列出 git 中任何分支的文件

> 原文：<https://dev.to/math2001/list-files-in-any-branch-in-git>

## 列出整个分支内容

```
~/my/repo $ git ls-tree mybranch -r --name-only
.gitignore
LICENSE
README.md
cli.js
demo.gif
index.html
live-svg.svg
package.json
server.js
tests/cli.js 
```

Enter fullscreen mode Exit fullscreen mode

## 只列出一个文件夹的内容

不需要`grep`！

```
~/my/repo $ git ls-tree mybranch:test -r --name-only
tests/cli.js 
```

Enter fullscreen mode Exit fullscreen mode

更多关于 [git ls-tree](https://git-scm.com/docs/git-ls-tree) 的信息。

希望它能节省你一些时间！ðŸ˜œ