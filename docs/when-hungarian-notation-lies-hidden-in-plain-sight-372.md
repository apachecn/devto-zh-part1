# 当匈牙利符号隐藏在显眼的地方

> 原文：<https://dev.to/scottshipp/when-hungarian-notation-lies-hidden-in-plain-sight-372>

像所有历史文物一样，[匈牙利符号](https://en.wikipedia.org/wiki/Hungarian_notation)曾经是有意义的。也就是说，在没有类型检查的情况下，这是有意义的。从一个你认为是整数的东西开始，然后以一个浮点数组结束。吓人！这种语言的用户选择了一个明智的想法，将变量的类型作为一个短前缀放在它的名字前面，以使程序更容易推理和防止错误。

在今天的大多数编程环境中，这是一种过时的需求，匈牙利符号具有相反的效果。语言和 IDE 一起使得(至少几乎)不可能错误地使用给定类型的变量。这使得好处消失了，负面的东西出现了:代码被随机的字母弄得乱七八糟。

到目前为止一切顺利。那么，为什么类似的模式仍然存在于阶级层面呢？举一个最明显的例子，在一些 Java 代码库中可以找到用“I”作为接口前缀的做法:

```
public interface IDataSource {
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

如果接口只是命名为`DataSource`就更好了。将`IDataSource`改为`DataSource`更好，因为没有必要过分强调调用代码使用接口。像 Java 这样的面向对象语言的标准实践是优先选择接口，因为它们比类有很多优势。如果你有兴趣深入了解为什么，可以看看约书亚·布洛赫的书*第 20 条*有效的 Java 。

当然，做出这样的改变可能会给你带来另一个问题。如果现有代码有`IDataSource`，接口，和`DataSource`，一个实现类，那么你做什么？我见过这样的代码，其中前缀只是被交换成类的后缀，比如，`DataSource`是接口，`DataSourceImpl`是实现类。这种方法不够优雅。当我面临这种情况时，我试图找到一个更具体的名称。

如果你想要一些好的命名的例子，只需要看看一些标准的 Java 库包，比如 java.util.Collections，你会发现像`List`这样的接口和像`ArrayList`或`LinkedList`这样的实现。只要稍加思考，您几乎总是会发现，您正在处理的实现类可以有一个类似的更具体的名称。在`DataSource`的例子中，一个名字可能和`DBDataSource`一样通用，但仍然比`DataSourceImpl`要好。

另一个相关的实践是在其名称中包含变量的类型。例如，您可能会看到`String userJson`或`int countInt`。谢天谢地，这种情况不太常见，但仍然很不幸。我认为当编辑器没有显示变量类型的机制时，这种情况最常见。如果您没有一个 IDE 可以通过鼠标悬停、ctrl+click 或其他方式显示变量的类型，那么很容易找到一个。我在 Visual Studio 代码、Atom 和 IntelliJ 中发现了这样的机制，仅举几个例子。

这种做法的一个更令人担忧的潜在原因是，变量的声明与它的使用相去甚远。如果在使用的同一个屏幕上没有看到声明了`userJson`的地方，那么可以考虑重构，把它们放在一起，去掉`json`部分。

通过提供有用的语言机制，强类型语言将有助于消除对这种“类型后缀”的需要。例如，在 Scala 中，有一个类型别名特性，它允许在给定的范围内使用类型的替换名。例如，您可能会发现声明`type PhoneNumber = String`的能力有好处，并且在给定的代码块中使用`PhoneNumber userCell`而不是更令人困惑的`String userCell`。

和往常一样，仔细考虑名字和类型将会产生更可读的程序。真正的考验是，你是否可以把一些代码交给没有上下文的人，他们仍然能够基本理解。与新开发人员或其他团队的人一起审查代码通常会产生有价值的见解，否则这是不可能的，因为我们都是沸水中的青蛙。由于我们已经习惯了这些术语，所以我们慢慢地发现了混淆术语的盲点。我喜欢总是问自己类似这样的问题，“十年后，如果我不再在这个代码库中工作，甚至不在附近，有人能打开这个代码并增强它或修复一个 bug 吗？”

如果其他开发人员喜欢在我的代码中工作，我总是认为这是最高的褒奖。