# 完成 CompletableFutures 流

> 原文：<https://dev.to/jklingsporn/complete-a-stream-of-completablefutures-3c3>

有时我发现自己处于这样一种情况:我必须执行一些异步任务，而当所有这些任务都完成后，又执行另一个异步任务。像往常一样，在这种情况下，我在 [stackoverflow](https://stackoverflow.com/questions/30025428/listfuture-to-futurelist-sequence) 中搜索了一个方法，排名最高的答案建议采用以下解决方案: