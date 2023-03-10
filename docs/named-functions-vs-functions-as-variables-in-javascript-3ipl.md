# 命名函数与 JavaScript 中作为变量的函数

> 原文：<https://dev.to/wesjd/named-functions-vs-functions-as-variables-in-javascript-3ipl>

我花了 1000 万美元在谷歌上找了一个关于这个想法的辩论，但是什么也没找到。

所以说真的，哪个对你来说更有可读性:

这个？【命名函数】

```
function foo (bar) {
    //some code
} 
```

Enter fullscreen mode Exit fullscreen mode

还是这个？(作为变量的功能)

```
const foo = bar => {
    //some code
} 
```

Enter fullscreen mode Exit fullscreen mode

请记住，除了这些函数之外，在同一个作用域中还有其他变量，并试着调试它！

就我个人而言，在我目前工作的地方，我们对主脚本函数(不在作用域内的函数)保持严格的关注，我称之为`named functions`。我已经在这个团队工作了大约 6 个月，我们已经有了一些人，他们最初认为函数是变量，现在他们改变了立场，认为命名函数可读性更好。

你的立场是什么？