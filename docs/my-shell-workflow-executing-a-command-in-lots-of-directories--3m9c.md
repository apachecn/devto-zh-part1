# 我的 Shell 工作流:在许多目录中执行一个命令。

> 原文：<https://dev.to/shriharshmishra/my-shell-workflow-executing-a-command-in-lots-of-directories--3m9c>

## 问题

您希望在许多目录中运行 shell 命令。例如，在我的例子中，我想在许多目录中执行一个 shell 命令(大约。200)来清理构建文件。

## 解

编写一个 for 循环并在 shell 上运行它对我来说很有用。

```
for d in ./*/ ; do (cd "$d" && mvn clean); done 
```

Enter fullscreen mode Exit fullscreen mode

## 解释

上面的 for 循环将在由路径`./*/`定义的目录列表的每个项目中运行`cd "$d" && mvn clean`。`$d`保存着目录名。您也可以创造性地使用复杂的 glob 来定义目录列表。

我希望这也能帮助你。