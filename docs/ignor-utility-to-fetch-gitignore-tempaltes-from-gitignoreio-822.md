# ignor，要获取的实用程序。gitignore.io 中的 gitignore 模板。

> 原文：<https://dev.to/thara/ignor-utility-to-fetch-gitignore-tempaltes-from-gitignoreio-822>

我在货上发表了 [`ignor`](https://crates.io/crates/ignor) 。

您可以使用`ignor`来完成此操作。

```
$ ignor rust intellij > .gitignore 
```

Enter fullscreen mode Exit fullscreen mode

## 为什么不用`cURL`？

你可以生成。gitignore 使用这样的命令。

```
$ curl -L -s https://www.gitignore.io/api/rust,intellij 
```

Enter fullscreen mode Exit fullscreen mode

然而，在我的 bash/zsh 历史中很难找到答案。

## 为什么不用 bash/zsh 函数？

我想按`space`键而不是`,`键:)
对我来说，按`,`键比按`space`键难。

* * *

感谢其他板条箱，LOC 不到 100，很好理解。
[公关迎宾](https://github.com/thara/ignor)。

编码快乐！