# 您希望您的业务逻辑操作离数据有多近？

> 原文：<https://dev.to/andreujuanc/how-close-to-the-data-you-like-to-have-your-business-logic-operations-86m>

我和我的一个朋友讨论商业 web 应用程序中的代码设计，当我们谈到逻辑层(或业务逻辑)的话题时，我们对在哪里实现它的偏好有很大的分歧:

*   他喜欢控制器，并保持他的逻辑。因此只使用数据库作为存储。

*   但另一方面，我一直在编写存储过程中的逻辑，所以我的控制器大多很瘦。

我的问题是，这是一个好的做法吗？存储过程是不是老是给我们带来麻烦？

我个人认为在代码中包含逻辑对于测试来说很好，但是当涉及到生产数据时，它可能会有一些性能问题。

想法？