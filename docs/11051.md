# 列出上次提交时更改的所有文件

> 原文：<https://dev.to/adamkdean/list-all-files-changed-in-last-commit-4iep>

通过在文件头使用`git diff-tree`列出所有在最后一次提交中更改的文件。

```
git diff-tree --no-commit-id --name-only -r HEAD 
```

Enter fullscreen mode Exit fullscreen mode

例如，如果您在上次提交时更改了 README.md:

```
adam@macbook:project (master) $ git diff-tree --no-commit-id --name-only -r HEAD
README.md 
```

Enter fullscreen mode Exit fullscreen mode