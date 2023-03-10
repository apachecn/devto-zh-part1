# 我能用我的语言做 FP 吗？

> 原文：<https://dev.to/ericnormand/can-i-do-fp-in-my-language>

我从有经验的程序员那里得到的一个非常普遍的问题是“FP 语言给了我什么我还没有的东西？我就不能用我的语言做 FP 吗？”这是一个伟大的问题，我希望我有一个同样伟大的答案。

我真的很同情这个问题。JavaScript 有 [`map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) 、 [`filter`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/filter) 、 [`reduce`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/reduce) 。Java 8 有[流库](http://www.oracle.com/technetwork/articles/java/ma14-java-se-8-streams-2177646.html)。似乎每种语言都在增加越来越多的功能特性，包括不可变的数据结构、一级函数、函数组合、析构和复杂的类型系统。

我同意:**你*可以*用任何语言进行函数式编程。**

函数式编程只是另一个范例。就像你可以用任何语言进行过程化编程，或者你可以用任何语言进行面向对象编程，或者用任何语言进行逻辑编程一样，你也可以用任何语言进行函数式编程。

但是等等。这回答了问题，但它回答了问题背后更深层次的假设吗？

所以让我问你这个问题:我能用 C 语言进行 OOP 吗？根据我上面的陈述，答案是“是”。事实上，我以前用 C 语言构建过小的对象系统。如果我能用 C 语言来做，为什么我还需要 OO 语言呢？我觉得我的对象系统给了我进行 OOP 所需的所有优势。但问题是:我能在不了解 OOP 的情况下用 C 语言编写 OOP 吗？我不这么认为。

这些范式被称为*范式*，因为它们是关于如何处理问题的整体观点。OOP 将问题分解成与消息通信的对象。FP 将问题分解成代表建模过程状态的数据。过程化将一个问题分解成一系列可以解决问题的步骤。每种范式都有不同的解决问题的方法。通过“在 Java 中做 FP”而没有掌握 FP，你暗示 FP 不是一个范例，它只是一组特性(不可变数据、纯函数、函数组合等)。

我看过很多次了。人们说他们在用 JavaScript 做 FP。他们的意思是，他们四处散布`reduce`和`map`，并使用一些纯函数。这很有价值。但除此之外，他们的代码是程序性的。他们没有学到范式，只学到了特性。

一门语言的价值在于它能让你沉浸在解决问题的方法中。它越是强化范例，你就越是被迫尝试使用范例的解决方案。你的语言功能性越强，你在用功能性方法处理问题时得到的训练就越多。OOP 和过程化也是如此。本质上，你得到了更多的实践，因为你不能依赖于你已经知道的其他范例。

所以让我说这个，更有争议的说法:在标有 *functional* 的语言之外(Clojure，Haskell，Elm，Scala，Erlang 等。)，我看不出正在做的函数式编程有多少深度。除非他们已经通过长时间使用函数式语言而熟悉了函数式编程，否则程序员只是借用了一点点 FP 范式，而忽略了最好的部分，即处理问题的角度的变化。

这并不是说在 JavaScript 中不可能学到真正的 FP。但是这需要大量的训练和帮助。语言不会引导你。你需要引导你自己或者找一个能引导你的人。

## 结论

所有的范例都很棒，因为一个[“观点值 80 智商分”](https://www.folklore.org/StoryView.py?project=Macintosh&story=Creative_Think.txt)。你可以用任何语言做任何范例。但是如果你不沉浸其中，学习一个范例是非常困难的。如果不使用一种强烈鼓励范式的语言，你很难沉浸在一种范式中。在这样做之前，您可能会借用范式的特性，但是您并没有以新的方式处理问题。简而言之，函数式编程不是一组特性。如果你想学它，就跳进深水区，沉浸其中。

如果你准备好潜水，请查看我教 Clojure 和函数式编程的 [PurelyFunctional.tv](https://purelyfunctional.tv) 。

摇滚起来。
埃里克