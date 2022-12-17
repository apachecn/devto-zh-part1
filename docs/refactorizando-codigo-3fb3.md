# 重构 código

> 原文：<https://dev.to/emamut/refactorizando-codigo-3fb3>

2016 年 1 月 14 日

> 真理总是在于简单，而不是在于多重性和混乱性艾萨克·牛顿

继我上一篇帖子([在尝试](https://codedebug.co/blog/2016/01/08/escribe-codigo-limpio-sin-morir-en-el-intento/)时写下干净的代码而不死】之后，我们继续这一系列的帖子☆正确的，这次我们将看到**重构**。

### 什么是重构？

[![](img/51c97df547cb0198bf40b49326ed77f6.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Xmeyl_Kh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/636/0%2A552k49Iz5hDKl7aS.gif)

[微软](https://msdn.microsoft.com/es-es/library/719exd8s.aspx)的定义是:

> *重构是指在不改变其行为的情况下通过改变其内部结构来改进代码的过程我认为这是不值得进一步解释的，现在让我们看看它的特点:*

### 不修正错误或添加功能

[![](img/4b7651de290fe455f65ece4212ded0ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--7InW_RGB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/960/0%2AWE24OIAt30Rk3qYJ.gif)

重构的目的是提高对代码的理解，或者改变代码的结构和设计，删除不必要的代码，因此我们应该在维护项目或添加新功能之前进行重构。

### 循证开发(TDD)

[![](img/3eac8b7be0ab9091004f3fa32fbd8e32.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ymBruHgX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/500/0%2ABTjob4GldA8NRfPJ.gif)

大多数现代编程语言都有执行**单元测试**(这是测试代码模块正常工作的一种方式)的套件，从而我们得以验证所做的更改以及对开发正常工作的影响。

### Como 函数 a？

[![](img/eb0663ab38cf311a73a422b665db2543.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jY29AF_I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/372/0%2A6CGboz0EEzs2T8Uk.gif)

让我们从理论到实践，我们有一种方法叫做*【get _ first _ element】*，它接收变量，确定它是否为*数组*，并返回第一个元素，否则返回 *false* :