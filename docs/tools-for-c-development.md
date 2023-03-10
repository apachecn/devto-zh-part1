# C++开发工具

> 原文：<https://dev.to/dwd/tools-for-c-development>

[![Tools](img/80e3b1d5dc236e2c1595387375622578.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yIfZ000I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z592jrmbdqsw0tglrysx.jpg)

由于我生命中的大部分时间都是在下水道下面某个黑暗的房间里度过的——或者有人称之为“系统编程”——我的工具集与 Web 开发的明亮、通风的世界完全不同，尤其是如果我在做 C++。众所周知，C++是一种很难使用的语言——它很低级，很复杂，如果不小心的话，很容易出错。

像[米](https://github.com/surevine/metre)和[斯皮芬](https://github.com/surevine/spiffing)这样的项目是很难跟踪错误的项目，并且当错误发生时是至关重要的。我已经有了一套很棒的工具来帮助避免和发现错误。

## 调试

[![Valgrind Logo](img/2ee1acce01b1629802fb531cbbd7ac21.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Utl4_otG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://valgrind.oimg/valgrind-link3.png)

其中最有用的当然是 **Valgrind** 。Valgrind 模拟处理器和内存，然后跟踪内存和操作的每个字节的附加信息。例如，它的 Memcheck 套件可以处理低级内存错误。这个字节的内存被初始化过吗？如果没有，并且我们后来使用它，那可能是一个 bug。这个字节在分配后被释放过吗？是放出来之后用的吗？

许多这类检查发现了在正常测试中可能不会触发实际崩溃的错误，甚至可能不是正常操作——但它们是错误，而且是危险的错误。因此，有一个像 valgrind 这样的工具可以在这种事情上提前触发是非常好的(并且确实可以在冻结的进程上启动一个调试器，以便您可以进一步研究)。

Valgrind 可以运行的其他分析器包括检查您是否锁定了线程服务器中的数据结构以及其他各种情况。最令人惊奇的是，它根本不需要任何工具——你可以在 Valgrind 中运行你的普通构建。

Valgrind 是开源的，可用于 Linux 和 Mac。

[Valgrind](http://valgrind.org/)

## 静态分析

静态分析是在运行源代码之前就对其进行潜在错误检查的过程。大多数编译器都内置了简单的静态分析工具，但是 **Coverity** 实际上是一种精神分析工具——它不仅会发现明显的错误，还会发现与函数正常用法的偏差，这可能会导致错误。例如，在 Metre 上，它指出我忘记了检查返回值(并且很有帮助地向我展示了我没有检查的其他地方)。它还指出，我已经将名为**的变量传递给了**，将名为**的变量从**传递给了一个函数，该函数将参数**从**和**传递给了**——尽管这不是一个 bug，但我发现它仍然令人印象深刻。

Coverity 是一项服务，开源项目可以免费使用 Coverity。

[扫描覆盖率](https://scan.coverity.com/)

覆盖性很好，但是将它作为本地“正常”构建的一部分运行通常更简单。Clang Static Analyzer 会发现各种各样的错误，尽管使用相当好的惯用 C++，即使你第一次尝试，它也不会发现太多。

开源 LLVM 项目的一部分。

[铿锵声静态分析仪](https://clang-analyzer.llvm.org/)

## 覆盖检查

我有点怀疑将代码覆盖率作为一个简单的度量标准——我认为将高代码覆盖率等同于高测试质量有点太容易了。

然而， **LCOV** 和**工作服**让你很容易就能发现你的代码覆盖率有多高，以及你在哪里缺少覆盖率。这意味着我可以创建测试用例来测试我发现缺失的代码路径。Linux 覆盖率是 Linux 测试项目的一部分，是一个构建在 GCOV 之上的代码覆盖率工具，全部在本地运行。连体工作服是一个 github 集成服务，与 Travis 配合得很好。

[工作服](https://coveralls.io/)
[LCOV](http://ltp.sourceforge.net/coverage/lcov.php)

## 模糊测试

American fuzzy lop，简称 AFL，是一个模糊测试器，旨在发现会导致应用程序崩溃的错误输入。这对于解析 XML 或 ASN 等格式的应用程序特别有用。

大多数模糊测试人员通过根据已知的规则改变输入数据来工作。AFL 的工作方式是像上面的覆盖工具一样检测你的代码，然后使用这种检测来使用遗传算法调整输入，并尝试探索你的所有代码路径。如果你想一想它在做什么，它看起来几乎是邪恶的——它不断地向你无助的程序输入信息，寻找新的可怕的方法来破坏它。

老实说，我能看到 AFL 变得更好的唯一方法是如果它结合了 Valgrind 发现几乎崩溃的能力。

[AFL](http://lcamtuf.coredump.cx/afl/)

## [这里](#ide)

无论您在堆栈中处于什么位置，您都可以从强大的开发环境中受益。

JetBrains 是 IDEA、Android Studio 和 Kotlin 背后的公司。 **CLion** 是他们的商业 C++ IDE，多多少少让 C++开发感觉像 Java，有完全的自动完成和即时错误高亮，还有集成的图形化调试器等等。

在我开始使用它之前，我从来都不是一个 IDE 爱好者；现在我发现我完全依赖它。它真的减少了编辑和运行之间的时间，并消除了它们之间的大量上下文切换。最后，自动补全功能非常好，当我处理其他项目时，我可以在很大程度上忘记项目的大部分内容，为手头的任务释放认知负荷。

Jetbrains CLion

## 其他

当然，我一直在寻找新的工具...还有一个评论区*就在那里*...