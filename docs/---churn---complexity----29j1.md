# 代码质量:“鞭挞”(churn)和复杂性(complexity)。如何追踪乐高西。

> 原文：<https://dev.to/phpprofi/---churn---complexity----29j1>

[![](img/fd2670222b9dbaee31d51e301fad6f6a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--EK7bZCeK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://matthiasnoback.nl/assets/2018/01/churn.png)

## 设备(代码复杂度)

复杂性经常通过计算每单位代码的 t0/t1/循环复杂度来衡量。可以通过考虑所有代码分支来计算指标。

代码的复杂性是几件事的指标:

*   t0t 1 代码块有多难理解。指标值很大，说明代码中有大量分支。在读取代码时，程序员必须跟踪所有这些分支，以了解应用程序在运行时可以“进入”的所有不同路径。
*   T0 测试这块 T1 代码有多难。指标值很大表示代码的许多分支，为了完全测试这块代码，所有这些分支都必须单独复盖。

* * *

http://phpprofi.ru/blogs/post/95 的читать

* * *

https://matthiasnoback.nl/2018/01/churn-legacy-code/