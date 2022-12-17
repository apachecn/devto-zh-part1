# 短路和干净代码

> 原文：<https://dev.to/mustardsauce/short-circuits--clean-code-1dn>

```
variable && newVariable = getStuff()

variable === anotherOne && (() => {
    //do stuff
})()

variable 
&& anotherOne
&& thisStuffIsTrue()
&& (() => {
    //wtf
})() 
```

Enter fullscreen mode Exit fullscreen mode

[短路](https://en.wikipedia.org/wiki/Short-circuit_evaluation)被认为是干净的代码/最佳实践吗？我最近在 JS 中看到过它们，尽管代码更短，但在我看来可读性要差得多。三元运算符的过度使用也是如此。