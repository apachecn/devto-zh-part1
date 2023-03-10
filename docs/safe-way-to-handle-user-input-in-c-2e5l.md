# 在 C #中处理用户输入的安全方式

> 原文：<https://dev.to/berviantoleo/safe-way-to-handle-user-input-in-c-2e5l>

你是新来的吗？世界上很多教程都在讲 c，为什么我要分享这个呢？因为，如果你不能正确地处理输入，尤其是在 C 语言中，你的程序将会崩溃。为什么会这样？你永远不知道什么时候有人会从用户输入的漏洞中破坏你的程序。我从 OWASP 引用了一些原则，在这里查看。

> 不要相信用户的输入。

在 C #中，当你不限制用户输入并且用户输入大于容器时，输入填满了你的容器，同时也填满了另一个容器。它也称为缓冲区溢出。用于处理用户输入的键，限制用户输入与您为该输入定义容器相同。我讲述了在 C 语言中使用标准输入的用户输入是多么有限(在 C++中使用 C 标准库时，你也应该注意)。

<figure>[![](img/d7d9966ad0bb7459ba6538dd8fc415ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q7TbniC---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AgIPwqZtwuPQWTGqr) 

<figcaption>照片由 [Ariel](https://unsplash.com/@arielbesagar?utm_source=medium&utm_medium=referral) 上 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

### 使用 scanf()

使用 scanf()处理用户输入总是安全的吗？当你正确定义格式时，我认为答案是肯定的。请参见下面的不同于代码。