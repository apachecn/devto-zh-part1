# Git 工作流脚本

> 原文：<https://dev.to/adamkdean/git-workflow-scripts-1km2>

下面是几个通用脚本，可以帮助你更快地使用 Git。把它们放在你的 bin 文件夹中，所以是`/home/user/bin`还是`C:\Users\Username\bin`取决于你的操作系统。记住如果是尼克斯，`chmod +x`他们。

`ga` - Git 添加脚本:

```
#!/bin/sh
git add -A 
```

Enter fullscreen mode Exit fullscreen mode

`gs` - Git 状态脚本:

```
#!/bin/sh
git status 
```

Enter fullscreen mode Exit fullscreen mode

`gpull` - Git 拉脚本(源主机):

```
#!/bin/sh
git pull origin master 
```

Enter fullscreen mode Exit fullscreen mode

`gpush` - Git 推送脚本(源主机):

```
#!/bin/sh
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

`gc` - Git 提交脚本:

```
#!/bin/sh
read -p "Commit msg: " desc
git commit -m "$desc" 
```

Enter fullscreen mode Exit fullscreen mode

`gfull` - Git 添加、状态、提交(带消息)和推送(原始主机)脚本。

```
#!/bin/sh
git add -A
git status
read -p "Commit msg: " desc
git commit -m "$desc"
git push origin master 
```

Enter fullscreen mode Exit fullscreen mode

如果我想出更复杂的东西，我可能会贴更多，但现在，这些可以节省很多时间。

如果你有什么经常使用的脚本，一定要分享出来！