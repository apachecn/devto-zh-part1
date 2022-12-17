# Python 中的引用计数和垃圾收集

> 原文：<https://dev.to/dzeban/reference-counting-and-garbage-collection-in-python-22jc>

不久前，我读过一个很好的故事, insta gram 如何为他们的 Python 应用程序禁用垃圾收集，内存使用量下降，性能提高了 15%。乍一看，这似乎有悖常理，但它揭示了 Python(即 CPython)内存管理的惊人细节。

## Instagram 禁用垃圾回收 FTW！

Instagram [是运行在 uWSGI 上的 Python/Django 应用](https://www.youtube.com/watch?v=66XoCk79kjM)。

运行 Python 应用程序 uWSGI 主进程派生并在子进程中启动应用程序。这应该利用 Linux 中的写时复制(CoW)机制——只要不被修改，内存就在进程间共享。共享内存很好，因为它不会浪费 RAM(因为它是共享的),并且因为多个进程读取同一个内存，所以它提高了缓存命中率。由 uWSGI 启动的应用程序大多是相同的，因为它是相同的代码，所以应该有很多内存在 uWSGI 主进程和子进程之间共享。但是，相反，共享内存在这个过程的开始就下降了。

起初，他们认为这是因为*引用计数*的缘故，因为对一个对象的每次读取，包括像代码对象这样的不可变对象，都会导致对该引用计数器的内存写入。但是禁用引用计数并不能证明这一点，所以他们选择了剖析！

在 [perf](https://dev.to/blog/perf/) 的帮助下，他们发现是垃圾收集器导致了大部分的页面错误——函数`collect`。

所以他们决定禁用垃圾收集器，因为仍然会使用引用计数来释放内存。CPython 为[提供了一个 gc 模块](https://docs.python.org/3/library/gc.html)，允许你控制垃圾收集。Instagram 的人发现使用`gc.set_threshold(0)`比`gc.disable()`更好，因为一些库(像他们的 msgpack)可以重新启用它，但`gc.set_threshold(0)`将收集频率设置为零，有效地禁用了它，并且它对任何后续的`gc.enable()`调用都是免疫的。

这样做是有效的，但是垃圾收集是在子进程退出时触发的，并且整整一分钟都在折腾 CPU，这是没有用的，因为这个进程即将被新的进程取代。这可以通过两种方式解决:

1.  添加`atexit.register(os._exit, 0)`。这表明在 Python 程序退出时，只需硬退出进程，无需进一步清理。
2.  在最近的 uWSGI 中使用`--skip-atexit-teardown`选项。

有了这些黑客，接下来的事情发生了:

*   uWSGI 主进程为应用程序启动一些子进程
*   当孩子启动时，GC 被禁用，所以它不会导致很多页面错误，从 CoW 中节省，并允许主节点和子节点共享更多的内存，并提供更高的 CPU 缓存命中率
*   当一个孩子死了，它会自己清理，但跳过最后的垃圾收集，节省关机时间，防止无用的 CPU 颠簸

## (Python)内存管理

我从这个故事中发现，CPython 有一个有趣的自动内存管理方案——它使用引用计数来释放不再使用的内存，并跟踪分代垃圾收集器来对抗循环对象。

这就是引用计数的工作原理。Python 中的每个对象都有引用计数器(`PyObject`结构中的`ob_refcnt`)，这是一个特殊的变量，当对象被引用(例如，添加到列表或传递给函数)时递增，当对象被释放时递减。当 ref 计数器的值递减到零时，它将被运行时释放。

引用计数对于自动内存管理来说是一个非常好且简单的方法。它是确定性的，避免了任何后台处理，这使得它在移动设备等低功耗系统上更有效。

但是，不幸的是，它有一些非常糟糕的缺陷。首先，它增加了在每个对象中存储引用计数器的开销。其次，对于多线程应用，引用计数必须是原子的，因此必须在 CPU 内核之间同步，这很慢。最后，引用可以形成循环，防止计数器递减，并且这样的循环对象永远保持分配。

无论如何，CPython 使用引用计数作为内存管理的主要方法。至于缺点，在大多数情况下并不可怕。存储 ref 计数器的内存开销并不明显——即使对于百万个对象，也只有 8 MiB (ref 计数器是 8 字节的`ssize_t`)。引用计数的同步不适用，因为 CPython 有全局解释器锁(GIL)。

唯一的问题是战斗周期。这就是 CPython 定期调用跟踪垃圾收集器的原因。CPython 的 GC 是分代的，即它有 3 代——0、1 和 2，其中 0 是所有对象诞生的最小代，2 是对象在进程退出之前存在的最老代。在 GC 中幸存下来的对象被转移到下一代。

将对象分成几代的想法是基于这样一种启发，即大多数被分配的对象都是短期的，所以 GC 应该比那些通常是永久存在的长期对象更频繁地尝试释放这些对象。

所有这些可能看起来很复杂，但是我认为 CPython 采用这样的方案是一个很好的权衡。有些人可能会说——为什么不像大多数语言那样只留下 GC 呢？嗯，GC 也有自己的缺点。首先，它必须在后台运行，这在 CPython 中是不可能的，因为 GIL，所以 GC 是一个停止世界的进程。第二，因为 GC 发生在后台，对象释放的确切时间框架是不确定的。

所以我认为对于 CPython 来说，使用引用计数和 GC 来互补是一个很好的平衡。

最后，CPython 不是唯一使用引用计数的语言/运行时。Objective-C，Swift 有编译时间[自动引用计数(ARC)](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AutomaticReferenceCounting.html) 。请记住，引用计数更具确定性，因此对于 iOS 设备来说，这是一个巨大的胜利。

Rust 也[使用参考计数](https://www.rust-lang.org/en-US/faq.html#is-rust-garbage-collected)

C++有智能指针，它基本上是带有引用计数器的对象，由 C++运行时析构。

Perl 和 PHP 等许多其他语言也使用引用计数进行内存管理。

但是，是的，现在大多数语言都是基于纯 GC 的:

*   Java/JVM
*   C#/CLR
*   去
*   哈斯克尔/GHC
*   红宝石/磁共振成像
*   许多其他人喜欢 Lisp

## 结论

CPython 有一个有趣的管理内存的方案——对象的生命周期通过引用计数来管理，为了避免循环，它使用了跟踪垃圾收集器。

## 参考文献

*   [原 Instagram 帖子](https://engineering.instagram.com/dismissing-python-garbage-collection-at-instagram-4dca40b29172)
*   [Python 的垃圾收集器模块](https://docs.python.org/3/library/gc.html)
*   [智能指针时代的垃圾收集](http://blogs.microsoft.co.il/sasha/2012/01/12/garbage-collection-in-the-age-of-smart-pointers/)
*   [如果有智能指针，为什么要进行垃圾收集](http://softwareengineering.stackexchange.com/questions/30254/why-garbage-collection-if-smart-pointers-are-there)
*   [GC 手册](http://gchandbook.org/)