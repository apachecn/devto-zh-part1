# 了解使用 Array reduce 方法的异步/等待

> 原文：<https://dev.to/gyandeeps/understanding-asyncawait-with-array-reduce-method-a74>

当我遇到一个用例时，我正在做一个项目，在这个用例中，我需要使用 Array 的`reduce`方法和一些`async/await`(【基于 T2】)网络调用。就在那时，事情变得混乱而复杂。让我们通过运行一个示例来了解如何将 async/await 与 array reduce 一起使用。

## 问题

使用 GitHub API 从 repo 获取所有提交消息**而不是**以`Fix:`或`Docs:`开始的 pull 请求对象。

我们将使用许多辅助方法来与 GitHub API 对话，并执行一些其他操作，但在这篇文章中我们不会讨论这些，因为我们希望将重点放在使用 array reduce 的`async/await`。

## 解

让我们创建一个名为`getNonSemverPatchPRs`的函数，它将根据 PR 的提交消息返回不符合 semver 补丁 PR 条件的 repo 中的所有 PR 对象。这里的期望是，当我们调用`getNonSemverPatchPRs`时，它应该返回一个`Promise`，并且这个承诺应该用一个 PR 对象数组来解析。

```
const getNonSemverPatchPRs = async () => {
    const allOpenPrs = await getAllOpenPRs();

    return allOpenPrs.reduce((collection, pr) => {
        // 1\. This is where we want to get all the commits of the PR in context
        // 2\. Then we want to see if the commit message of the first commit message starts with `Fix:` or `Docs:`
        // 3\. If yes then ignore it otherwise add it to the collection.
    }, []);
}; 
```

### 获取 PR 的所有提交

为了完成步骤 1，我们需要执行一个网络调用来获取 PR 的所有提交。现在这个电话是基于承诺的。因为我们必须调用`await`，所以我们需要调用 reduce 处理函数`async`。

```
const getNonSemverPatchPRs = async () => {
    const allOpenPrs = await getAllOpenPRs();

    return allOpenPrs.reduce(async (collection, pr) => {
        const allCommits = await getAllCommitsForaPR(pr.number);

        // 2\. Then we want to see if the commit message of the first commit message starts with `Fix:` or `Docs:`
        // 3\. If yes then ignore it otherwise add it to the collection.
    }, []);
}; 
```

### 检查第一次提交的提交消息

现在我们将检查第一次提交的提交消息，看它是以`Fix:`还是`Docs:`开始。这个调用是对助手函数的同步调用。

```
const getNonSemverPatchPRs = async () => {
    const allOpenPrs = await getAllOpenPRs();

    return allOpenPrs.reduce(async (collection, pr) => {
        const allCommits = await getAllCommitsForaPR(pr.number);

        const isNotSemverPatchPR = checkCommitMessageForPatch(allCommits[0]);
        // 3\. If yes then ignore it otherwise add it to the collection.
    }, []);
}; 
```

### 如果请购单不是永久补丁请购单，则添加至集合

现在，我们将检查它是否不是一个永远补丁公关，然后添加到收集或减少，否则忽略它。

```
const getNonSemverPatchPRs = async () => {
    const allOpenPrs = await getAllOpenPRs();

    return allOpenPrs.reduce(async (collection, pr) => {
        const allCommits = await getAllCommitsForaPR(pr.number);

        const isNotSemverPatchPR = checkCommitMessageForPatch(allCommits[0]);

        if (isNotSemverPatchPR) {
            collection.push(pr);
        }

        return collection;
    }, []);
}; 
```

## 异步函数处理程序 reduce 内部出现问题

*   **认为:**根据你对`async/await`和数组`reduce`的了解，你会认为它会不断地将`pr`对象推送到`collection`并返回`collection`，以便 reduce 的下一次迭代可以使用它并不断地向集合中添加内容。

*   **现实:**reduce 回调函数是一个异步函数，所以它总是返回一个`Promise`。因为它返回了一个`Promise`,`collection`参数的值不是一个数组，而是来自前一次执行的一个`Promise`。

*   **解决方案:**由于`collection`总是包含一个`Promise`，那么我们需要解析这个承诺，以获得最终将成为我们的集合的响应，然后我们可以继续向它推送内容，然后将它作为函数的一部分返回。

    *   使 reduce 的初始值成为一个虚拟的 resolved `Promise`，然后我们可以继续解析每个调用返回的承诺。
    *   在函数内部创建一个集合，可以通过解析传入的承诺来提取该集合。

```
const getNonSemverPatchPRs = async () => {
    const allOpenPrs = await getAllOpenPRs();

    return allOpenPrs.reduce(async (previousPromise, pr) => {
        const collection = await previousPromise;
        const allCommits = await getAllCommitsForaPR(pr.number);

        const isNotSemverPatchPR = checkCommitMessageForPatch(allCommits[0]);

        if (isNotSemverPatchPR) {
            collection.push(pr);
        }

        return collection;
    }, Promise.resolve([]));
}; 
```

## 结论

*   我建议运行上述示例，并尝试设置断点，以便更好地理解流程。你可以随意使用 [JSBin](https://jsbin.com/weginet/1/edit?js,console) 来玩。
*   `async`函数总是返回一个`Promise`，这就是为什么 reduce 函数开始累加前一个`Promise`的原因。当这个承诺完成时，它会在函数中返回真正的集合。

我仍然在努力解决这个问题，但是我想写一些关于这个的东西，这样我就可以帮助那些遇到这个问题的人。请在下面的评论中留下您的反馈。