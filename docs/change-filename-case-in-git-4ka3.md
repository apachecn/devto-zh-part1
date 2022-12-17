# 更改 git 中文件名的大小写

> 原文：<https://dev.to/adamkdean/change-filename-case-in-git-4ka3>

Git 不区分大小写。所以要在 git 中改变文件名的大小写，你必须跳过一个(小)圈。

```
git -f <source> <destination> 
```

Enter fullscreen mode Exit fullscreen mode

示例:

```
git -f src/helloworld.js src/HelloWorld.js 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！