# 如何在接口和抽象类之间做出选择？

> 原文：<https://dev.to/suhas_chatekar/how-do-you-choose-between-an-interface-and-abstract-class>

几年来，我一直在采访中问这个问题。我从人们那里听到了很多有趣的答案。所有这些答案都帮助我丰富了我的知识。如果你用谷歌搜索这个话题，你会发现很多有趣的文章。这篇关于 MSDN 的文章很好地讨论了这个话题。总结一下那里的建议和我看到的其他几篇文章，下面是支持抽象类的理由

1.  如果你有一些子类不需要实现的行为的默认实现，使用抽象类
2.  如果你的契约有可能随着时间的推移而改变，那么最好选择抽象类。因此，如果您正在使用一个抽象类，并且需要向您的抽象类添加一个新方法，您可以愉快地添加它，而不会破坏使用该类的任何代码。对于接口来说，情况并非如此。

这些就是支持界面的原因

1.  因为 C#不支持多重继承，所以您不能从两个抽象类继承您的类。在这种情况下，接口是您唯一的选择。
2.  如果从抽象类继承的所有类之间没有默认的或共同的行为，那么接口可能是一个更好的选择。

我个人不认为这些给出了一个完整的画面。

# 这个推理有什么问题？

首先，上面的建议主要围绕特定语言支持的语法，而不是围绕语义(例如，如果需要多重继承，就使用接口)。我的意思是，接口的什么定义从根本上区别于抽象类？
第二，我感觉上面的标准太超前了。我的意思是，它们都依赖于你知道你的设计在未来将如何成形。有时，我可能对我的设计在未来将如何成形有一些想法，但大多数时候我没有足够的清晰度来预先知道

1.  我是否需要从多个抽象类继承
2.  是否会有某个契约的默认实现
3.  为了实现变更，我是将一个新方法添加到一个契约中，还是完全定义一个新的契约

所以，如果你不知道你的软件要走哪条路，你就没有办法基于这些原因来决定接口或者抽象类。

# 那么我们如何决定呢？

最近，我一直在使用 below heuristic 来决定什么时候使用接口/抽象类，我对此感到非常兴奋，因为它在大多数时候都是有效的。

> 接口代表能力，抽象类代表类型

换句话说

> 实现接口代表可以做的关系，从(抽象)类继承代表是-是关系

为了详细说明这一点，让我们考虑下面两个类

```
public class Camera 
{ 
  public void Shoot() 
  { 
    //Take a picture here 
  } 
} 

public class Gun 
{ 
  public void Shoot() 
  { 
    //Hit the target 
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

`Camera`和`Gun`都能射，那是他们的能力。但它们两者不是同一类型的东西，它们是完全不同的。所以像下面这样的界面在这里更有意义

```
public interface IShootable 
{ 
  void Shoot(); 
} 

public class Camera : IShootable 
{ 
  public void Shoot() 
  { 
    //Take a picture here 
  } 
} 

public class Gun : IShootable 
{ 
  public void Shoot() 
  { 
    //Hit the target 
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你像我一样学过面向对象编程，那么你会记得下面这个经典的形状例子

```
public abstract class Shape 
{ 
  void Draw(); 
} 

public class Rectangle : Shape 
{ 
  public void Draw() 
  { 
    //Draw a rectangle here 
  } 
} 

public class Circle : Shape 
{ 
  public void Draw() 
  { 
    //Draw a circle here 
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

`Rectangle`和`Circle`从`Shape`继承在这里非常有意义，因为`Rectangle` / `Circle`是`Shape`的一种类型。

# 在关闭

随着软件的增长，新的特性开始涌入，旧的特性不断变化，您可能会对自己说“哦，上帝，我不应该在这里使用抽象类”。不要试图预测谁将重用哪个代码，而要关注于确定它是一个你正在抽象的能力还是一个公共类型。如果你觉得这种想法很难，那就弄清楚你想要塑造的是一种“能行”的关系还是一种“是-是”的关系。