# 在 GitHub 上将 git 克隆的存储库推到您自己的存储库中

> 原文：<https://dev.to/dance2die/push-git-cloned-repository-to-your-own-on-github-1ili>

** **更新**:2019 年 4 月 21 日**

尤金·卡拉塔耶夫 & [诺亚·彼得森](https://dev.to/chiefnoah)都提供了比这篇文章更好的方法。

看看下面。

[![karataev profile image](img/818ea436e6b76fd77bd17899d4452b6b.png) ](/karataev) [ Eugene Karataev ](/karataev) • [<time datetime="2019-04-21T02:55:35Z"> Apr 21 '19 </time>](https://dev.to/karataev/comment/a906) 

没有必要移除旧的原点并创建新的原点。您可以只更改现有的原点 url:
`git remote set-url origin NEW_URL`

[![chiefnoah profile image](img/b2075b6f2c4ff244a696da6a9f323aa8.png) ](/chiefnoah) [ Noah Pederson ](/chiefnoah) • [<time datetime="2019-04-21T02:42:00Z"> Apr 21 '19 </time>](https://dev.to/chiefnoah/comment/a904) 

与其创建一个新的回购协议，为什么不创建一个 fork，然后以同样的方式设置遥控器呢？git 历史应该是相同的，GitHub 仍然会将其作为分叉的存储库进行跟踪

* * *

您从 GitHub 中 git 克隆了一个库，而没有派生它。

问题是你做了一堆修改，想把它发布到一个新的 GitHub 库。

完成它需要哪些步骤？

### TL；速度三角形定位法(dead reckoning)

> 移除原始遥控器并添加您自己的遥控器

### 场景

假设您正在利用 GitHub 上浮动的所有 javascript 样板，因为手动设置简单代码所需的 Webpack 和环境有点太多了。

### 问题

但是当您试图将代码提交回 GitHub 时，您会得到一条错误消息，提示您没有权限，因为克隆的 repo 属于另一个用户。