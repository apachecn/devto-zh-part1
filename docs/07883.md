# 接口的问题，以及如何解决它

> 原文：<https://dev.to/dean/the-problem-with-interfaces-and-how-go-fixed-it>

# 界面很棒

他们真的很聪明。接口允许简单、优雅的编程。让我们使用 Java 来快速了解如何使用接口。

```
package mypackage;

public interface Edible {
  public void eat();
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，用一个`Burger`类来实现`Edible`。

```
package mypackage;

public class Burger implements Edible {
  public void eat() {
    System.out.println("Burgers, yum!");
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们可以在任何看到`Edible`的地方使用我们的`Burger`类，比如在方法参数中！

# 接口的问题

但是假设我们使用的是一个库，这个库有一个类叫做`Salad`。下面是`Salad`的样子:

```
package theirpackage;

public class Salad {
  public void eat() {
    System.out.println("I'm being healthy!")
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以`Salad`有一个`eat()`方法，但是因为对方库甚至不知道`Edible`接口的存在，显然无法实现。这个问题以前肯定遇到过，那就说说 Go 是怎么修复的吧。

# Go 如何修复接口

根据 Go 接口的工作方式，您不需要声明接口实现。如果实现了适当的方法，就实现了接口。这在围棋中非常突出，是一个关键特性。让我们用之前的例子。

首先，让我们重新制作我们的`Edible`界面。

```
package mypackage

type Edible interface {
  Eat()
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们创建一个实现`Edible`的`Burger`结构。

```
package mypackage

type Burger struct {}

// How to define a method in Go
func (b Burger) Eat() {
  fmt.Println("Burgers, yum!")
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，在创建`Burger`结构时，我们从未在任何地方确认过`Edible`。这是因为*任何有`Eat()`方法与之关联的*类型都被认为是`Edible`。这意味着即使来自其他库的类型也可以是`Edible`，只要它们有一个`Eat()`方法！

这意味着来自不同包的这个`Salad`结构也是`Edible`。

```
package theirpackage

type Salad struct {}

func (s Salad) Eat() {
  fmt.Println("I'm being healthy!")
} 
```

Enter fullscreen mode Exit fullscreen mode

这显示了 Golang 有多棒。我强烈推荐观看他们的视频[Go 之旅](https://www.youtube.com/watch?v=ytEkHepK08c)，该视频展示了 Go 必须提供的所有出色功能。

编辑:真的很喜欢这个帖子的讨论！我在这里没有提到的一个关键点是，Go 中的接口应该用来描述简单的模式。[这里的](https://youtu.be/ytEkHepK08c?t=8m21s)是来自[Go 之旅](https://www.youtube.com/watch?v=ytEkHepK08c)的一个片段，希望它能描述 Go 中的接口与 OOP 中的接口相比应该如何使用。