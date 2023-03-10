# 不可变纸张

> 原文：<https://dev.to/ericnormand/immutable-paper>

我以前说过，不可变数据更容易推理，因为它更像真实世界。这似乎违反直觉，因为世界是可变的。我可以移动东西，擦掉黑板，甚至改变我的名字。那么，不可变数据比可变数据更像真实世界吗？

这里有两个故事可以解释我的观点。想象一下，我拿一张干净的活页纸，有蓝线的那种。我拿出一支又好又粗的记号笔，用大写字母写下了单词*香蕉*。然后我把它折叠起来，递给苏珊。“这很重要”，我说，“所以仔细听着。把这个放在你的口袋里，回家之前不要让任何人碰它。然后就可以拿出来了。”

苏珊不明白为什么，但她把它放进了口袋。她确保没有人整天在她的口袋里。当她回到家，她发现那张纸就像她记得的那样折叠在那里。她打开它，读了起来。

上面会写些什么？(这不是一个技巧问题。)

每个人都会知道答案。是*香蕉*这个词。但是你怎么知道的？你怎么这么确定？事实上，这个世界实现了我们的期望很多次，一整天，而我们甚至没有想到这一点。**我们从小就知道宇宙的这些趋势。**这种推理甚至可能融入我们的神经系统结构。我们对世界的理解是直觉的。你可能会说“我们可以对此进行推理”。

现在，让我告诉你另一个故事。假设我构造了一个对象 P(用于纸张)，并在其上设置了一个属性为字符串`"banana"`。我将该对象传递给另一个对象 S 上的方法(针对苏珊)，该方法将该对象存储在私有属性中。对象 S 保持该属性受到保护，从不允许任何东西写入它，并且它确保永远不会调用可以写入它的方法。过了一会儿，object S 打印出 p 的内容。

**你期望在 P 中存储什么？**(这也不是什么技巧性的问题。)

答案是“不知道”。可能是`"banana"`。如果对象 P 被引用它的其他东西修改了，它可能是其他东西。我们不知道它会说什么，因为它是可变的，会产生“超距作用”这样的魔法效果。我们不能再对此进行推理。

我们宇宙中的许多物体都在不断地自行变化。行驶中的汽车的位置，铀盒子里的东西(通过衰变)，蜡烛的亮度。每次我们看到它，我们都希望它有所不同。但同样，许多物体基本上是静止的，除非受到其他东西的作用。一栋房子，一杯水，一张纸。该对象实际上以我所关心的任何方式保持不变。这是不可变对象比可变对象建模更好的部分。

问题是，一旦你共享了对同一个可变对象的两个引用，**你就远离了我们所生活和了解的世界**。对同一个对象的两次引用就像两个人同时在口袋里放了同样的纸。您可以像 Erlang 一样不共享任何东西(当它在参与者之间传递时，所有的东西都被复制)，但是 Erlang 也选择让事情不可变。您也可以在存储对象之前复制它们。这种做法需要严格的纪律，而且**会首先取消通过使用可变数据获得的任何效率优势**。

可变对象很重要。他们可以模拟非常普通和有用的东西。例如，您可以将文件柜建模为可变对象，因为您可以取出和放入东西。但是你会想把里面的文件建模成不可变的，因为它们在里面的时候不能改变。两者兼得才是成功之道。

Clojure 使得区分这两者变得非常容易。像列表、向量、哈希映射和集合这样的数据结构在 Clojure 中都是不可变的。Clojure 也有一个包含可变事物的[工具箱:原子、引用、代理和变量。这些对于建模共享的东西的**当前状态和改变**是很好的。](https://purelyfunctional.tv/guide/clojure-concurrency-the-ultimate-guide/)

嗯，我希望这些故事解释了为什么不可变对象在许多有用的情况下确实像真实世界的对象一样，以及这是为什么人们声称 Clojure 代码更容易推理的关键部分。如果你喜欢这个想法，并想进一步探索，请查看纯 Functional.tv 时事通讯。这是每周一期的时事通讯