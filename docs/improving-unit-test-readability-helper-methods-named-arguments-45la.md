# 提高单元测试的可读性:助手方法和命名参数

> 原文：<https://dev.to/xpirit/improving-unit-test-readability-helper-methods-named-arguments-45la>

“鲍勃大叔”在[干净代码:敏捷软件工艺手册](https://www.amazon.co.uk/gp/product/0132350882/)中写道:

“花在阅读(代码)和写作上的时间比远远超过 10 比 1……(因此)让代码更容易阅读也就更容易写作。”

所以当你写下一段代码的时候，想想这个问题。你需要确保你的代码对其他人来说是容易阅读和理解的，你很少只为自己写代码。

## 可读单元测试

单元测试代码与“生产”代码没有区别。可读性在这里是关键，因为不清楚的单元测试将会被不信任、忽略甚至可能被删除。

### 粗大排列节

考虑下面的单元测试，在一个假想的`MovieService`中测试`GetHighestRatedMovies`方法: