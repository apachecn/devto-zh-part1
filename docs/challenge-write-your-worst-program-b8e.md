# 挑战:写出你最糟糕的程序

> 原文：<https://dev.to/r0f1/challenge-write-your-worst-program-b8e>

大家好，
你们可能知道，JavaScript 中没有办法确定一个数是否是另一个数的倍数，所以我写了这个函数。

```
function isMultiple(big, small){
    return (big/small + "").indexOf('.') < 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在您也可以在代码中使用它。不客气有其他的建议给我吗？我在寻找函数，可以减轻我的编程生活。任何编程语言都是受欢迎的。

**编辑**:一些快速编辑，因为似乎有混乱。这篇介绍性的文章应该是讽刺性的。我知道有一个`%`算子。我知道这个功能没有意义，而且效率非常低。挑战在于，你应该提供一些(有意或无意的)错误的或者过于复杂、低效或无用的代码。