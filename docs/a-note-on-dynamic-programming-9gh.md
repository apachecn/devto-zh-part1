# 关于动态规划的一个注记

> 原文：<https://dev.to/huytd/a-note-on-dynamic-programming-9gh>

这是一本关于动态编程的笔记，摘自《算法设计手册》一书。我发现它真的很有帮助。

## 为什么动态编程这么难？

> *   Until you understand dynamic programming, it looks like magic.
> *   You must know the trick before using it.

## 绝招

> *   Dynamic programming is a technique to efficiently realize recursive algorithm by storing partial results.
> *   The trick is to see if the naive recursive algorithm repeatedly calculates the same subproblem.
> *   If yes, store the answer to each sub-question in a table and look it up instead of recalculating.
> *   Start with a recursive algorithm or definition. Only when we have a correct recursive algorithm do we worry about speeding it up with a result matrix.

## 关于如何学习的建议

> *   Dynamic programming is best learned by studying examples carefully until things start to click.