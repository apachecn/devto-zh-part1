# 使用 TestCafe | Pararell 执行的 E2E 测试

> 原文：<https://dev.to/chrisvasqm/e2e-testing-with-testcafe--part-2-1lfg>

*[先前](https://dev.to/chrisvasqm/e2e-testing-with-testcafe-17jl)在“E2E 测试用 TestCafe”...*

我们讨论了以下主题:

*   E2E 和测试咖啡馆是什么。
*   正在安装 NodeJS、TestCafe、IDE/editor。
*   设置项目。
*   进行我们的第一次测试。
*   在单一浏览器中运行我们的第一个测试。

这次我们将了解

# 并行运行测试

这可以通过使用如下命令来实现:

```
testcafe -c NUMBER-OF-BROWSERS BROWSER PATH-TO-TEST-FILE 
```

Enter fullscreen mode Exit fullscreen mode

但是(总有但是！)如果我们现在运行它，将会发生一些奇怪的事情...

我们的“检查创始人姓名”测试不会失败，但我们将能够看到第二个 Chrome 浏览器将什么也不做。

你可能会想，多么懒惰的浏览器。

这实际上是一种非常有趣的并行执行方式。

由于在我们的`devto.js`文件中只有一个测试方法，第二个浏览器并没有偷懒，它只是无事可做。他为什么要浪费时间做和第一个浏览器一样的测试？

不相信我？让我们添加另一个测试:

(我将向您提供完整的测试方法，这样您就可以将-pasta 复制到您的编辑器中，以便快速再次运行测试)

```
test("Filter articles by discuss tag", async (t) => {
    const discussTag = Selector('span').withText('#discuss');
    const discussTitle = Selector('h1').withText('Discussion');

    await t
        .click(discussTag)
        .expect(discussTitle.exists).ok();
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在使用这个命令:

```
testcafe -c 2 chrome tests/devto.js 
```

Enter fullscreen mode Exit fullscreen mode

确保将每个 Chrome 窗口分开，这样你就可以同时看到它们是如何工作的。

酷，但是如果我们有更多的测试会发生什么？我们是否应该通过与现有测试数量相同的`-c #`？大概不会。如果你试图同时打开太多的浏览器，你将会使用更多的资源。

它实际上要做的是，如果我们有 3 个测试，我们给它一个`-c 2`，无论哪个浏览器先完成它的测试，都将进行第三个测试。如果我们进行 4 次、5 次或更多次测试，情况也会如此。

这在我们的测试套件在开发的后期成长之后非常有用。这将帮助你把 E2E 测试的时间减少一半，甚至更多！

在第 3 部分中，我们将运行多个浏览器，然后运行每个浏览器的多个实例。

[![chrisvasqm](img/e4a5dffbeebdbe975aabe7903c567401.png)](/chrisvasqm) [## 用 TestCafe |多浏览器测试进行 E2E 测试

### 克里斯蒂安瓦斯奎兹 1 月 15 日 182 分钟阅读

#testcafe #e2e #testing #javascript](/chrisvasqm/e2e-testing-with-testcafe--part-3-2io7)