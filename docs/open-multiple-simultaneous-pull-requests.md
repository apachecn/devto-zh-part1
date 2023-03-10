# 打开多个同步拉取请求

> 原文：<https://dev.to/svenluijten/open-multiple-simultaneous-pull-requests>

分叉库是开源的核心，每个人做的都有一点点不同。因此，让我们来看看我过去是如何处理同一个存储库上的多个拉请求的，它所带来的问题以及这些问题的解决方案。

## 问题

假设我想创建两个拉请求来解决存储库`octocat/test-project`上的两个独立问题。很多人会创造他们的第一个很好:

1.  在 GitHub 上派生存储库
2.  克隆分叉的存储库
3.  在`master`分支中进行更改
4.  推到分叉的存储库
5.  打开拉取请求

好的，这很有效。但是，添加第二个拉请求会改变动态。对同一个`master`分支进行更改会影响最初的(第一个)拉请求，所以我们不要这样做。现在`master`的分支将包括你已经为 PR #1 所做的改变。有些人可能会告诉你做以下事情:

1.  再次分叉 GitHub 上的存储库(这将创建一个`test-project-1`存储库)
2.  克隆分叉的存储库
3.  在*中对*库的`master`分支进行修改
4.  推到分叉的存储库
5.  打开拉取请求

显然，如果您想打开第三个、第四个甚至第五个拉请求，这将变得不明智。我是在`test-project-2`还是`test-project-4`做了那个改变？

## 解

你如何应对这种情况？分支！最初克隆第一个存储库时，首先为您的 PR 解决的问题创建一个分支:

```
$ git checkout -b first-pull-request 
```

这将创建并检查`first-pull-request`分支。为 PR #1 做你的改变，并把它们推送到 GitHub。你现在可以在 GitHub 上打开你的第一个 PR 了。

添加第二个 PR 再简单不过了:

```
# Ensure you're on master!
$ git checkout master
$ git checkout -b second-pull-request 
```

同样，做出你的改变并把它们推送到 GitHub。现在您可以安全地打开第二个拉请求，而不必担心与第一个冲突！