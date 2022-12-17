# 拉请求:一个简单的工作流

> 原文：<https://dev.to/jmfayard/pull-requests-a-simpleworkflow-dha>

声明:我不是一个 git 忍者，我很确定很多人已经就这个话题写了更复杂的东西。我唯一的资格是过去犯了很多错误。

因此，希望能有所帮助，以下是我对如何简单有效地提出拉式请求的看法:

*   我克隆了最初的回购协议。应该指向最新最棒的，而不是我那很快就没人维护的叉子
*   我在 github 上点击“fork ”,将我的叉子添加为遥控器
*   我贡献了一个失败的测试来展示你的想法
*   我打开一个拉动请求来开始讨论

讨论可能有很多结果:我可能已经请求了一些可能的东西，或者超出了项目的范围，或者这是一个坏主意，或者这是一个维护者将尽快实现的好主意，或者这是一个我准备自己实现的好主意。

我们假设后一种情况。我在本地勤奋工作。几天或几周后，我又回到了拉取请求中。我有一些反馈，非常好，我改进我的代码。接下来发生的事情，尤其是维护良好的快速项目，是我在这一点上远远落后于我分叉的提交。在这一点上，我一直停留在过去，不得不做太重的 git 体操。(看你 git rebase！)

以下是完成工作的 4 步工作流程:

*   拉取并合并来源/主数据(提醒:来源是项目的回购)
*   解决冲突，更新内容，然后提交
*   `git reset --soft origin/master`然后再犯。
*   强制推送到我的回购

步骤 2 和 3 中的提交将具有相同的内容，从项目维护者的角度来看，git 历史将是干净的，并且整个事情具有很好的防止白痴的特性。

## TL；速度三角形定位法(dead reckoning)

这是我的白痴解决方案，可以让 PR 比`origin/main`
高一次

```
git fetch
git merge origin/main

# if conflicts
echo "resolve conflicts in your IDE"
git commit -a -m "resolve conflicts"
# end if

git reset --soft origin/main
git commit -a -m "⚡️ implement xxx"
git push --force 
```

Enter fullscreen mode Exit fullscreen mode