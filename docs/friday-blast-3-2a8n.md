# 星期五爆炸#3

> 原文：<https://dev.to/horia141/friday-blast-3-2a8n>

因为在度假，这周的爆炸时间较短。

[《UUID 简史》(2017)](https://segment.com/blog/a-brief-history-of-the-uuid/)——我原本以为是一篇关于 UUID 的短文，结果却变成了对它们悠久历史的深度探究。甚至还涉及到一些代码考古学。有趣的事实:电话号码是我们在交流中使用的第一个唯一标识符。

最好的图标是文本标签(2015)——最好的总结是标题本身。

[在 PostgreSQL 中实现状态机(2017)](http://felixge.de/2017/07/27/implementing-state-machines-in-postgresql.html)——值得一提对于 Postgres 的高级用法，本文谈谈直接在 Postgres 中实现状态机的各种方式。不仅仅是拥有一个`state`列，让应用程序代码处理状态管理。

[多项式中的布尔逻辑(2017)](https://jeremykun.com/2017/07/24/boolean-logic-in-quadratic-polynomials/) -一种将布尔公式编码为多项式的简洁方式。你把一个有变量 x_1，x_2，\dots，x_n 的布尔表达式 A 转换成 x_1，x_2，\dots，x_n 中的多项式 A_P，通过把每一个`AND`变成乘法，每一个 F 项的否定变成 1 - F_P，`OR`由[德摩根定律](https://en.wikipedia.org/wiki/De_Morgan%27s_laws)处理。文章中也有两三个这种转换的实际应用的例子。

* * *

[1]至少是现实生活中的 CS 理论。