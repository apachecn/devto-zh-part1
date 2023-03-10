# git 工作流——在 forks、squash 和 rebase 之间

> 原文：<https://dev.to/lirantal/git-workflows---between-forks-squash-and-rebase-c3b>

Git 非常灵活，这就是它的伟大之处，每个人都可以采用自己的工作流。因此，在没有进入一场激烈的战争之前，我希望收集一些意见:

场景:对于一个将受益于 CI/CD 的 SaaS 产品，团队(4-12 名工程师)与 github 私有回购合作，利用拉式请求将功能分支的工作合并到一个单独的`master`分支。

1.  使用单个 repo 和其中的所有团队开放功能分支，与团队成员在自己的 repo 中分叉和工作以创建 PRs 的 github 工作流相比，有什么优点/缺点？
2.  如果拉-请求合并策略是挤压(使用 PR 上的 GH 按钮完成)，那么如果它的唯一目的是挤压提交，那么基本上没有理由让成员重新基于他们自己的分支。