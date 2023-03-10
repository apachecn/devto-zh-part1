# 使用 mingit 加快速度

> 原文：<https://dev.to/sendra/git-faster-with-mingit>

我写 [mingit](https://github.com/evansendra/mingit) 是因为我想到了作为日常程序员我们在命令行上使用 git 的次数，以及我们浪费了多少时间一次又一次地输入相同的命令。我想为什么我们总是打字

```
git commit -m "my super dope commit" 
```

Enter fullscreen mode Exit fullscreen mode

当简单的

```
g c "my super dope commit" 
```

Enter fullscreen mode Exit fullscreen mode

够了吗？为什么在我写`g`的时候，我的终端已经知道我在做什么了？

所以我写了 mingit。用 npm - `npm install -g mingit`可以安装[的一小段 shell 代码。](https://www.npmjs.com/package/mingit)

下面是它的简写:

```
g a .                   // git add .
g b other-branch        // git branch other-branch
g c "made some changes" // git commit -m "made some changes"
g commit --amend        // it also works with regular subcommands
g co master             // git checkout master
g co mas<TAB><TAB>      // auto-complete still works! ðŸ™Œ
g d                     // git diff
g f                     // git fetch
g i                     // git init 
g m hotfix              // git merge hotfix
g pll                   // git pull
g psh                   // git push
g s                     // git status 
```

Enter fullscreen mode Exit fullscreen mode

[查看更多！](https://github.com/evansendra/mingit)

[![](img/9e03c572566f20d523daadcb29dcb2da.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Wnwd5-xY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/http://i.imgur.com/lylmPzU.gif)

已经有了其他的 git 别名，但是我想要一个 git 别名:

1.  git 命令本身的别名
2.  允许后续命令的别名
3.  没有破坏制表符补全(例如，用别名键入 git checkout 仍然提供您可以签出的东西)

官方的 git 别名没有完成#1，但是它完成了#2 和#3。

我发现的任何其他化名都符合第二条，但不符合第三条，也可能不符合第一条(都是交易破坏者)。

如果你想帮助改进这个工具，我很乐意看到[你的拉动请求](https://github.com/evansendra/mingit)！