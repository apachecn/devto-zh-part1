# 使用静态引用处理配置更改

> 原文：<https://dev.to/vladimirwrites/handling-configuration-changes-using-static-references-1d6e>

[![Header](img/3b2794fec2a16f6ad0b1106a17c1c5f6.png)T2】](https://i.giphy.com/media/3otPoQqrl8oggqDKg0/giphy.gif)

在讨论配置更改时的数据保存时，有两种情况需要讨论。首先，如果有固定数量的小对象需要在变化中存活，使用`onSaveInstanceState`，就这样，故事结束，问题解决。然而，当您需要保存大小未知的大对象时，真正的问题就来了。根据您的需求，有几种解决这个问题的正确方法。但这不是这篇课文要讲的。这篇课文将会讲述一种非常流行的，也是非常错误的方法。我们都知道这是一个坏消息，但不是所有人都知道为什么。互联网上有很多关于这个话题的问题，几天前我也被问到这个问题。

> 如果您持有我的活动中的数据的静态引用，以便在屏幕旋转时保留它们，会怎么样？这有什么不好？

“别这样，这样不好！内存泄露！危险！🚨”这是我的第一反应。但随后他/她说:“是的，是的……我知道，但我会非常小心，我将只保存数据，不保存`Context`或任何与`Context`相关的内容。还不好吗？”你可以打赌它是！但这让我想到:

> *“怎么还是不好？”*

为什么静态引用你的`Activity`里面的一些数据不好？我想了一会儿，然后`💡.turnOn()`。因为🥁🥁🥁 **的内存泄露**而糟糕。

[![Fail](img/14e9dead0188bb4edf71badb5091bfd9.png)T2】](https://i.giphy.com/media/aVtdz7iNVPI1W/giphy.gif)

然而，这不是典型的 Java/Android 内存泄漏，Leakcanary 会警告你。这种泄漏的内存仍然可以被访问，尽管不再需要它，但操作系统不能对它进行 GC。在`Activity`中静态引用一个对象的主要问题是很难知道什么时候释放它。