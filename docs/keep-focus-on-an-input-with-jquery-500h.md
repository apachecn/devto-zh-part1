# 使用 jQuery 将焦点放在输入上

> 原文：<https://dev.to/adamkdean/keep-focus-on-an-input-with-jquery-500h>

最容易导致脱发的问题通常都有最简单的解决方案，这可以说是我最新的治疗男性型脱发的捷径。

我正在做我的永久项目，它的一部分需要一个输入始终保持专注*。窗外的用户体验，这是必须的，但是`.blur()`是不可取消的，`.focusout()`也是。这意味着你不能只在`.blur()`函数中调用`.focus()`。你必须欺骗它。*

 *首先设置初始焦点:

```
$("#input").focus(); 
```

Enter fullscreen mode Exit fullscreen mode

然后设置`re-focus`:

```
$("#input").blur(function() {
    setTimeout(function() { $("#input").focus(); }, 0);
}); 
```

Enter fullscreen mode Exit fullscreen mode

工作很愉快。*