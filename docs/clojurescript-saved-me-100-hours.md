# ClojureScript 帮我节省了 100 个小时

> 原文：<https://dev.to/buntine/clojurescript-saved-me-100-hours>

五年前，我开始摆弄 Clojure，一个运行在 JVM 上的 Lisp。这种语言在当时还是有点深奥的。它*毕竟是*口齿不清！

学习 Clojure 并不太难，因为我已经花了几年时间研究 Scheme。生成分形、斐波那契数列(当然),并通过 [SICP](https://mitpress.mit.edu/sicp/) 和[简单地设计](https://www.cs.berkeley.edu/~bh/ss-toc2.html)教科书。事实上，我想我可能仍然在通过我的[答案库](https://github.com/buntine/Simply-Scheme-Exercises)帮助伯克利大学的一些一年级计算机科学学生。嘿伯克利，荣誉学位怎么样？哦，来吧...

从一开始就很明显，Clojure 将会是一件大事。尽管语言设计者 Rich Hickey 决定坚持使用 S 表达式，但 Clojure 提供了一组比普通链表丰富得多的数据结构。它们是不可变的和持久的，这给了它们不变性的线程安全性和可变性的性能特征。整个 Java 互操作也非常重要...这意味着 Clojure 程序员可以访问过多的现有 Java 库。

我用 Clojure 编写的第一个重要软件是一个基于文本的游戏，名为[中世纪外星人屠杀](https://github.com/buntine/Medieval-Alien-Massacre)。它的灵感来自于一个名为 Dunnet 的老游戏，有趣的是，它仍然是 Emacs 编辑器的默认包。写这个游戏非常有趣，尽管我犯了一个错误，在我写故事之前选择了一个标题并发布了游戏《T2》。成品不涉及任何屠杀，没有发生在中世纪。然而，它确实包含了外星人，所以我并没有完全错！

但是，更重要的是，与我的非技术朋友分享这个游戏有点困难，因为它要求他们安装 Java 并在 shell 中运行。我决定(有一天)用 Javascript 重写游戏，这样我就可以使用浏览器了。就易于分发而言，很难打败 Javascript。

我的游戏就这样结束了。不完整和未知。唉，这就是电脑程序员的苦恼...直到，也就是一个朋友跟我提到一个叫做 [ClojureScript](https://github.com/clojure/clojurescript) 的项目开始有些流行。ClojureScript 是一个 Clojure to Javascript 编译器，这意味着，至少在理论上，我可以直接将中世纪外星人屠杀编译成 Javascript，省去了手动编译的麻烦！

我知道它不会“只是工作”。我使用 Java 的线程库来暂停执行，从终端执行用户 IO 等等。所以我开始一行一行地检查代码，标出所有我认为可能无法编译的部分。这个过程相对简单。ClojureScript 将 Java 互操作替换为 Javascript 互操作，例如:

```
(.  Thread  sleep  10) 
```

Enter fullscreen mode Exit fullscreen mode

被简单地替换为:

```
(.setTimeout  js/window  f  10) 
```

Enter fullscreen mode Exit fullscreen mode

我用一个简单的 HTML `input`替换了所有的 IO，这个简单的 HTML`input`包装在一个附加了提交事件的`form`中。我认为大约 85%的核心代码保持不变。语言解析一点都没变！

总的来说，这个过程花了我下班后三个安静的晚上。我也趁机给游戏起了一个更贴切的名字:[月球居民](https://github.com/buntine/MoonDweller)。我猜，我会花至少 100 个小时用 Javascript 写我自己的版本。

哦，你猜怎么着？！你可以直接在你的浏览器上播放！

[现在玩月球居民](http://moondweller.io/)

祝大家黑客愉快。