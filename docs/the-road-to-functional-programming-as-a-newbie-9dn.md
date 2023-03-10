# 新手的函数式编程之路。

> 原文：<https://dev.to/antonrich/the-road-to-functional-programming-as-a-newbie-9dn>

所以我开始阅读“函数式编程”。实践与理论”。我和我的一个朋友去图书馆学德语。我住在约什卡奥拉。俄罗斯中部的一个城市。我没想到会在我们城市的图书馆里找到那样的一本书。老实说，这本书是个意外。我不是在找它。

我想引用书中的一些内容。这本书也令人困惑，因为作者使用了我不熟悉的术语，如 gotos、结构化编程和应用编程。良好的应用程序设计被解释为函数式程序设计的另一个名称。但是 gotos 是什么？

这是我喜欢的一句话:

为什么算术表达式的符号对数学家如此有益？原因似乎相当微妙和根本。它体现了结构化的原则，这是我们通过将复杂问题分析成更简单的子问题来掌握复杂问题或控制复杂局面的所有尝试的基础，这些子问题之间具有清晰而狭窄的界面。

C.A.R .嘶哑(1973，15)

* * *

12 月 17 日更新:

我继续阅读了作者给出的使用函数式编程的六个理由，那就是:

1) **函数式编程省却了无处不在的赋值操作。正如结构化编程通常被称为“无 goto-less 编程”一样，函数式编程也可以被称为“无赋值编程”。赋值仍然存在，但是对程序员来说是隐藏的。**

我不知道什么是赋值操作和无跳转编程。所以让我们去谷歌上找找吧。

PC mag 百科是这样说的:

无跳转编程

不使用 goto 指令编写程序，这是结构化编程中的一个重要规则。goto 指令指向程序的不同部分，但不保证返回。不使用 goto，而是使用称为“子例程”或“函数”的结构，这些结构在完成时自动返回到调用指令之后的下一条指令。

这还不足以让我理解。

这是自由词典的定义:

结构化编程(从无 Goto-less 编程重定向而来)

使用自顶向下的方法设计和编码计算机程序。

当然还有维基百科[https://en.wikipedia.org/wiki/Goto](https://en.wikipedia.org/wiki/Goto)

哦，还有一整篇关于结构化编程的文章[https://en.wikipedia.org/wiki/Structured_programming](https://en.wikipedia.org/wiki/Structured_programming)

当我在维基上阅读 goto 的文章时，我有去洗手间的冲动。当我在那里的时候，我有一个好主意(你有好主意吗？)注意，当我进入不必要的深度时，比如我正在做的 goto，我会做一个标签，比如# dive _ deeper _ when _ I _ have _ the _ time _ to _ kill _ or _ the _ context _ requires _ me _ to _ know _ that _ stuff

在我这种情况下，更好的办法是深入理解没有任务的编程意味着什么:

*   [https://en . Wikipedia . org/wiki/Assignment _(computer _ science)](https://en.wikipedia.org/wiki/Assignment_(computer_science))
*   [https://software engineering . stack exchange . com/questions/144042/how-a-pure-functional-programming-language-manage-without-assignment-statements](https://softwareengineering.stackexchange.com/questions/144042/how-a-pure-functional-programming-language-manage-without-assignment-statements)

我已经试着读了那两篇文章，但我还是不明白。希望以后能得到。

2)函数式编程通过提供修改现有程序的行为和组合现有程序的机制(高阶函数),鼓励在更高抽象层次上进行思考。

3)函数式编程为大规模并行计算机编程提供了范例。赋值的缺失、求值顺序的独立性以及对整个数据结构进行操作的能力为这些机器的编程提供了范例。

4) FP 在人工智能中的应用。

5) FP 在可执行的规范和原型实现中是有价值的。FP 简单的底层语义和严格的数学基础，以及它的高表达能力，使它成为指定程序预期行为的理想工具。

6)第六个原因我完全不懂。所以也许以后我会明白。

* * *

我会边看书边更新帖子。我希望理解潜在的原则，因为作者说即使你可能不使用函数式编程，但是知道这些原则将会改进你的风格。