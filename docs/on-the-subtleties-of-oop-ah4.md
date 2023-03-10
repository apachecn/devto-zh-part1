# OOP 的微妙之处

> 原文：<https://dev.to/martinhaeusler/on-the-subtleties-of-oop-ah4>

说到面向对象，说我是一个爱好者并不为过。一个精心制作的面向对象系统是一件艺术品。然而，遵循好的面向对象设计的所有原则是具有挑战性的。这篇文章应该展示为什么我们在编程面向对象系统时永远不能放松警惕。结果比我最初打算的要长得多，但是请在这一点上容忍我——它值得你花时间。

# 代码

在我的一个项目中，我需要做一些数学编码，特别是需要结对工作。尽管`org.apache.commons.lang3`有优秀的元组类(我强烈建议使用它们！)我需要一些额外的铃铛和哨子，最终我自己写了它们。

下面是我的结对类的大致样子(没有我需要的额外东西):

```
public class Pair<A,B> {

  private final A first;
  private final B second;

  public Pair(A first, B second){
    Objects.requireNonNull(first); 
    Objects.requireNonNull(second);
    this.first = first;
    this.second = second;
  }

  // getters for first and second

  public int hashCode() {
    return this.first.hashCode()*31 + this.second.hashCode();
  }

  public boolean equals(Obj other){
    if(other == null) { return false; }
    if(other == this) { return true; }
    if(other instanceof Pair == false) { return false; }
    Pair<?,?> p = (Pair<?,?>)other;
    return this.first.equals(p.getFirst()) 
        && this.second.equals(p.getSecond());
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

上面的类本身工作得很好。后来在我的项目中，我也需要与三元组一起工作。遵循“不重复自己”(干)的原则，我是这样实现的:

```
public class Triple<A,B,C> extends Pair<A,B> {

  private final C third;

  public Triple(A first, B second, C third) {
    super(first, second);
    Objects.requireNonNull(third);
    this.third = third;
  }

  // getter for third

  public int hashCode() {
    return super.hashCode()*47 + this.third.hashCode();
  }

  public boolean equals(Obj other){
    if(other == null) { return false; }
    if(other == this) { return true; }
    if(other instanceof Triple == false) { return false; }
    Triple<?,?,?> t = (Triple<?,?,?>)other;
    return this.first.equals(t.getFirst()) 
        && this.second.equals(t.getSecond()) 
        && this.third.equals(t.getThird());
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

我设法重用了两个字段和一堆其他的东西，太好了！...至少我是这么认为的。剧透:上面的代码有一个巨大的缺陷。如果你能一眼就看出来了，我向你脱帽致敬。我没有。

事情是这样的:**上面的代码破坏了 Java 编程的一个基本契约。**

# 这个问题

写完这些类后很长一段时间，我在一个大约 100，000 行代码的代码库中搜索一个错误，其中一些非常复杂。在某些情况下，代码表现不稳定，我的 JUnit 测试抱怨。但是我找不到问题。

最终，经过几个小时的调试，我得出了以下结论:

```
 // complex logic here ...

  Set<Pair<String, Long>> pairs = calculate();
  Pair<String, Long>> pairToSearchFor = ...;

  if(pairs.contains(pairToSearchFor)){
    // ... path A
  } else {
    // ... path B
  } 
```

Enter fullscreen mode Exit fullscreen mode

紧张的调试表明，在这个方法中，`path A`从未被调用过。我不明白为什么会这样，显然我在调试场景中寻找的那对应该在集合中。我在调试器中检查了集合的条目，然后我突然明白了。

该集合仅包含`Triple`个。

在我们的实现中，可以用一个`Triple`来代替一个`Pair`，Java 编译器不会抱怨。但问题是。假设我们有两个元组:

```
Pair<String, Long> pair = new Pair<>("Hello", 123L);
Triple<String, Long, Long> triple = new Triple<>("Hello", 123L, 144L);

pair.equals(triple); // returns true
triple.equals(pair); // returns false 
```

Enter fullscreen mode Exit fullscreen mode

我们违反了`Object#equals(...)`的合同。这就是在哈希集中找不到该条目的原因。`Object#equals(...)`(像任何其他等式关系一样)必须*对称*、*自反*和*传递*。在我们的例子中，它是不对称的。我们**在语法上履行了**契约(即我们以让`javac`高兴的方式实现了方法)，但是我们**在语义上**违反了契约。在这篇博客文章中，这一点看起来很明显，但是祝你好运，在一个大的代码库中发现这个错误。

# 我哪里做错了？

我的第一个冲动是运行一个修复程序，调整`Pair`的`equals`方法。很明显，三个一组永远不可能等于一对。但是`Triple`是`Pair`的子类，所以`instanceof Pair`不会帮助区分这两者。我也觉得不合适。我意识到如果不让这个`equals(...)`方法知道它的子类，我就没有机会让它工作。而且根据 OOP 设计规则，一个类应该**永远**不知道它的子类(除非是`abstract`)。

那又怎么样呢？我们没有遵循每一个最佳实践吗？我们是否在面向对象的原则中发现了一个缺陷？我们打破矩阵了吗？

# 解

当我应用 DRY 原则来共享`first`和`second`字段时，我只关注**语法**。我遵循了这个原则，因为它很容易**。不重复这些字段似乎是个好主意。然而，我忘记了面向对象的一个原则，当涉及到继承时，它压倒了所有其他的原则。只有当子类*实际上是基类的语义专门化*时，才使用继承。继承可以成为实现枯燥设计的工具。但不是唯一的，也不总是适用的。**

 **`Triple`和`Pair`共用两个字段吗？是的，他们有！不过是*的语法*罢了！

每一个`Triple`在每一种情况下也是一个`Pair`吗？肯定不是！

为了恰当地使用继承，第二个结论需要是`true`。然而，它需要比第一个多得多的精神努力来检查。是关于你类的**语义**。`javac`这里帮不了你。我思维中的本质缺陷是，我专注于编写尽可能少的代码行，但我最终不仅违反了`Object#equals(...)`的契约，也违反了面向对象的核心规则之一——起初甚至没有注意到。这里被违反的规则被称为[利斯科夫替代原理](https://en.wikipedia.org/wiki/Liskov_substitution_principle)。

# 瓶子里的消息

这篇文章的要点是:

*   在 OOP 中，语法是达到目的的一种手段。在你的决策中关注语义。
*   规则、原则和最佳实践有时会相互矛盾。确保你知道哪一个比另一个更重要。
*   永远不要仅仅为了在语法上共享公共字段或方法而使用继承。
*   永远不要只是盲目地实现一个接口。阅读该死的手册。
*   千万不要对`Object#hasCode()`或`Object#equals(...)`的实现掉以轻心。它**会**回来缠着你。

感谢你的阅读，我希望这篇文章有一天能为某人节省大量在调试器中浪费的时间。**