# 现代化 Java——语言特性的愿望清单(第 2 部分)

> 原文：<https://dev.to/martinhaeusler/modernizing-java---a-language-feature-wish-list-part-2-7c0>

在“现代化 Java”系列的第二部分中，我们将考虑 C#、JavaScript (ES6)和其他语言的特性。该系列的第一部分可以在[这里](https://dev.to/martinhaeusler/modernizing-java---a-language-feature-wish-list-part-1-eik)找到。

# 特征来自[红宝石](https://www.ruby-lang.org/en/)

Ruby 是一种脚本语言，尤其以 web 框架“ruby on rails”而闻名。就脚本语言而言，它是一种非常干净的语言，也是我第一次遇到`yield`关键字和协程概念的语言。基本上，`yield`允许你退出一个函数的当前控制流，当它下次被调用时，你从你停止的地方继续:

```
// this is how coroutines could look in Java
public Iterator<Number> powersOfTwo(){
   int current = 1;
   while(true){
      yield current;  // note the new "yield" keyword here
      current *= 2;
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子是一个无限序列的生成器。注意，我们在这里使用`while(true)`循环是*而不是*消耗 CPU 周期。因为我们在每次迭代中退出控制流，所以每次调用`ìterator.next()`只执行一次迭代。返回的迭代器是*隐式*，不需要定义。这个概念也被 ES6、Python、C#等很多语言所改编，人们正在把它发挥到极致(hello， [Redux Saga](https://github.com/redux-saga/redux-saga) ！).与本博客系列中的许多其他特性一样，这是一种生活质量的增强，可以在标准 Java 中“模拟”。然而，我真的认为这将是非常有用的。

# 特性来自 [C#](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/)

程序员经常把 C#贴上“Java 的下一次进化”的标签。事实上，这两种语言有许多共同点，如果不是因为标准库中的差异，人们很可能会编写一个 transpiler，将 C#源代码文件转换为 Java 源代码文件，反之亦然。全面的讨论超出了本文的范围。C#提供了许多 Java 中没有的有趣的语言特性。

## 分部分项类

在 C#中，可以将一个类标记为`partial`。这允许你将一个类拆分成多个文件，但是编译器会把它们当作一个文件:

```
// in file "myClassPart1.cs"
public partial class MyClass {

}

// in file "myClassPart2.cs"
public partial class MyClass {

} 
```

Enter fullscreen mode Exit fullscreen mode

它不同于一个`import`语句，因为最终，二进制文件中只有一个类。“为什么会有人想这么做？”你可能会问。这很有用的主要原因是代码生成。例如，有强大的 WYSIWIG UI 生成器可以生成 C#源代码文件(例如，VisualStudio 中集成了一个)。如果您曾经有过处理代码生成的可疑乐趣，那么您将知道必须手动编辑自动生成的文件的痛苦。问题是:一旦您重新运行生成器，您的手动更改就会丢失。在 Java 世界中，已经有人努力“标记”手写代码的各个部分，这样生成器就不会去管它们了(例如，参见 [EMF](https://www.eclipse.org/modeling/emf/) 的代码生成工具)。有了部分课程，这些烦恼就永远消失了。生成器控制一个文件(类的一个*部分*)，而你手写的代码进入一个完全不同的文件，这个文件恰好是同一个类的另一部分。您可以确保您的手写更改不会被某个自动生成器覆盖，因为它们驻留在生成器不知道的不同文件中。这是一个只涉及 Java 编译器的特性，运行时保持不变，因为最终只产生一个`*.class`文件。Java 是代码生成的流行目标，拥有分部类将有助于大大减轻生成代码的痛苦。

## `event`关键词

这是 C#的一个相对较小的细节，但我个人很喜欢这个细节:关键字`event`。你多久用 Java 写一次这样的代码:

```
private Set<EventListener> eventListeners= new HashSet<>();

public void registerEventListener(EventListener listener){
   this.eventListeners.add(listener);
}

public void removeEventListener(EventListener listener){
   this.eventListeners.remove(listener);
}

public void fireEvent(Event event){
   for(Listener listener : this.eventListeners){
      listener.onEvent(event);
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

真的很重复。如果你有一个处理 5 个不同事件类的类，那么上面的代码需要重复修改四次。在 C#中，你会得到上面的所有代码，如下:

```
public event MyEvent MyEvent; 
```

Enter fullscreen mode Exit fullscreen mode

如果要添加事件监听器:

```
myClass.MyEvent += myListener; 
```

Enter fullscreen mode Exit fullscreen mode

...并在内部触发事件:

```
this.MyEvent(event); 
```

Enter fullscreen mode Exit fullscreen mode

听着，妈，不行！这是一件非常小的事情，但是它消除了许多样板代码。一般来说，使用观察者模式是否是一个好主意是一个完全不同的讨论。

## 元组

在 C#的最新版本中，增加了对元组的本机支持。这让你可以很容易地构建、传递和解构成对、三元、四元，只要你能想到的。下面是它的样子:

```
(int count, double sum, double sumOfSquares) = ComputeSumAndSumOfSquares(sequence); 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了什么？`ComputeSumAndSumOfSquares`返回一个三元组，包含计数、总和以及平方和。我们在一个方法调用中接收所有三个值。如果我们对这三个都不感兴趣，我们可以用`_` :
替换变量声明

```
(_, double sum, _) = ComputeSumAndSumOfSquares(sequence); 
```

Enter fullscreen mode Exit fullscreen mode

它很简单，也很优雅，遗憾的是它不存在于 Java 中。

## `nameof`

一个好的编程习惯是编写前提条件，以确保您收到的参数确实符合规范。这允许您的方法快速失败并提供精确的错误消息。现在，如果你考虑这个代码:

```
public long sum(Iterator<Long> values){
   if(values == null) { throw new IllegalArgumentException("Argument 'values' must not be NULL!"}
   // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

...您会注意到`values`出现了两次*:一次作为参数名，一次在字符串中。这本身没问题，但是如果我用*重命名*这个变量会怎么样呢？字符串文字不会改变，因为 IDE 不知道这两者之间的语义关联(您也可以在字符串中启用替换，但这有其他问题...).C#提供了一个优雅的解决方案:* 

```
public long Sum(IEnumerator<Long> values){
   if(values == null) { throw new ArgumentException("Argument '" + nameof(values) + "' must not be NULL!"}
   // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，`nameof`消除了将变量名硬编码成字符串文字的需要。`nameof`将传递的变量的*名*作为字符串产生。另一件小事，但很有用，特别是对于错误消息。

# 来自 JavaScript 的  特性(特别是 [ES6](http://es6-features.org/#Constants)

ES6 在语法方面对 JavaScript 做了一些非常好的增强。

## 对象解构

其中最有用的一个叫做*对象解构*。你多久用 Java 写一次这样的代码:

```
MethodResult result = someMethod();
int size = result.size();
byte[] data = result.getData();
User author = result.getAuthor(); 
```

Enter fullscreen mode Exit fullscreen mode

ES6 在这里消除了很多仪式:

```
const { size, data, author } = someMethod(); 
```

Enter fullscreen mode Exit fullscreen mode

这与 C#元组类似，但又不完全相同。ES6 在`someMethod`的结果对象中寻找同名字段，并将它们分配给新的局部变量。解构器实际上可以做更多的事情(比如在不存在的情况下重命名和分配默认值)，但这是另一篇博文的内容。虽然这在 Java 中不会很顺利(因为需要识别和调用 getters 等。)，拥有这些东西肯定是有用的。

## 从对象到布尔的隐式转换

当编写 JavaScript 代码时，尽管我通常讨厌隐式转换，但有一个结构我很喜欢使用:

```
if(this.header){
   // render header
} 
```

Enter fullscreen mode Exit fullscreen mode

注意上面代码中的`header`不是一个布尔值，它是一个数据结构。通过在`if`语句中使用它，我们检查它是否是`null`(或`undefined`，但那是另一个故事)。这种通过检查空性从 Object 到 boolean 的隐式转换肯定是有用的。然而，在处理数值时，它在 JavaScript 中确实有一些问题，因为数字`0`也隐式地转换为`false`；在我看来，这种约定不应该超越像 C 这样的低级语言。检查空值是 Java 中非常常见的任务，让它变得更快更容易似乎是个好主意。

# 来自 C/C++

你有没有在 Java 中遇到过这样的情况，你想写一个可配置大小(以兆字节为单位)的缓存？那你麻烦大了。在 Java 中，你不知道*实际上*有多大。通常你不需要关心，但是如果你遇到这样的困境，这些问题将会卷土重来。你可以通过反射*估计*一个物体的大小，但这是一个缓慢而昂贵的操作。或者，您可以通过代理使用 Java 插装，但是这会使您的应用程序的部署变得复杂，并且一般来说会感觉*错误*，因为您只想做一些简单的事情，比如测量内存中对象的大小。我真正希望在 Java 中看到的是 C/C++提供的开箱即用的东西，也就是`sizeof`关键字。我意识到这在 JVM 中不是一件容易的事情，但是对于在 JVM 上编写“客户机”的程序员来说，这几乎是不可能的。

# 来自[哈斯克尔](https://www.haskell.org/)

Haskell 是一种函数式语言，在许多方面是 OCaml 的精神继承者。

## 列表理解

生成列表是编程中的一项常见任务。Haskell 通过引入*列表理解*使这方面变得非常简单。比如:

```
[(i,j) | i <- [1,2], j <- [1..4] ] 
```

Enter fullscreen mode Exit fullscreen mode

...会产生对子`[(1,1),(1,2),(1,3),(1,4),(2,1),(2,2),(2,3),(2,4)]`。尝试一下嵌套的 for 循环，你就会明白为什么上面的语法很棒。

## 局部应用

在 Haskell 中，您可以*部分应用*函数，在这个过程中产生新的函数。比如:

```
add x y = x + y
addOne = add 1
add 3 4 -- produces 7
addOne 6 -- also produces 7 
```

Enter fullscreen mode Exit fullscreen mode

`addOne`现在是一个带有*一个*参数的函数，增加了常数 1。今天你也可以用 Java 做一些类似的事情:

```
BiFunction<Integer, Integer, Integer> add = (a,b) -> a + b;
Function<Integer, Integer> addOne = (a) -> add(1, a); 
```

Enter fullscreen mode Exit fullscreen mode

...除了你需要一个 *lot* more 仪式。这也类似于 JavaScript 中的`bind`函数，以及缺省值参数(在几种语言中都有)。即使部分应用在函数式编程中使用得最广泛，它也是一个容易“提取”的方面，因为它不依赖于函数式编程的其他特征(例如懒惰求值)。理论上，它可以在任何允许函数(或方法或过程或...)来电。我不知道为什么很少人采用这个简洁的功能。

# 结论

我希望您喜欢这次语言特性之旅。Java 在很多方面都是非常好的语言，但是它需要继续发展。在这个博客系列中，我试图给出一个“其他人都在做什么”的概述。我错过了什么重要的事情吗？在 Java 中，您还希望看到哪些本系列根本没有涉及到的语言特性？请在评论中告诉我:)

感谢阅读！*