# “let”vs“const”与函数的心理质量

> 原文：<https://dev.to/katzy687/let-vs-const-and-the-mental-mass-of-functions-1dn>

### 让是新的 var？

因此，这种启示是在一次代码审查中呈现给我的。“你为什么在这里用`let`？我在这个函数中没有看到它有任何变化？”

你看，我开始解释，几乎我看过的所有 es6 教程都将`let`声明为新的事实上的`var`(带有块范围)。将`let`作为您的首选变量，除非您确实知道您不需要重新分配它——在这种情况下，您可以使用`const`。对我来说，这似乎是一个好方法，让我的变量保持灵活，直到我选择明确地限制它们。

### 常数第一法

“不，不，不”他开始说。“默认情况下，你应该使用`const`，只有当你想让变量*改变*时，你才应该使用`let`。一看到`let`，我就开始寻找它会被重新分配到哪里。如果你使用`const`，我可以简单地忘记它，少了一件要考虑的事情。”

[![mind blown](img/35ee3190cd7c382207b9aa80200094c7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--xs2p5Mdq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://prodimage.images-bn.com/pimages/2940014355193_p0_v1_s550x406.jpg)

这对我意义重大，以至于我有点惊讶它不是默认推荐。这种方法也正好符合我正在尝试采用的函数式/声明式编程方法，因为它清楚地陈述了意图并最小化了干扰。这似乎是一个微小的变化，但是在具有多个参数和重新分配的更复杂的函数中，这个微小的变化可以非常有助于减少函数的认知负荷。

现在，我不再盲目地使用`let`，以防以后可能需要重新赋值，而是默认使用`const`，如果我想在函数中途重新赋值，我会返回并将其更改为`let`。

感谢您阅读我在**dev to**上的第一篇帖子！