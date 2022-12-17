# 编写和阅读面向对象代码的二分法

> 原文：<https://dev.to/developerscode/the-dichotomy-of-writing-and-reading-object-oriented-code>

我二十年职业生涯中的大部分时间都在编写面向对象的代码。尽管我已经这样做了很长时间，我仍然发现自己被一个特殊的迂回的习惯所困扰。

**我通常不会*开始*用面向对象的方式写代码。相反，我经常将面向对象重构作为编辑过程的一部分。**

这里有一个非常简单的例子。假设我有一个名为`User`的类。大概是这样的:

```
public class User
{
    public readonly string FirstName;
    public readonly string LastName;

    public User(string firstName, string lastName)
    {
        FirstName = firstName;
        LastName = lastName;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在我的应用程序的其他地方，远离`User`类定义，我有一个`User`对象的实例并输出一些信息。也许，我想返回一个用户已经完成任务的声明。我会这样写:

```
public string GetTaskResponse(User myUser)
{ 
   return myUser.FirstName + " " + myUser.LastName + " has completed the task!";
} 
```

Enter fullscreen mode Exit fullscreen mode

当我需要使用对象中的数据组装一些逻辑时，我倾向于在当前*需要*逻辑的位置组装逻辑。例如，如果我在一个显示用户全名的视图类中工作，我将首先在视图类*中写出这一点逻辑。**我在需要工作的地方工作**。这种方法对我来说是最自然的。但是，它也不是一种特别面向对象的思维方式。*

在我完成这个阶段后，我会寻找可以打破这个逻辑的地方。我调查我是否复制了另一个方法中已经存在的一些逻辑来保持代码干燥。我还想出在我工作的类中哪里可以提取逻辑并用更具表达性的方法替换它们。

回到我们的例子，我可能将创建用户全名的字符串部分提取到一个新方法中，仍然在我工作的地方:

```
public string GetTaskResponse(User myUser)
{ 
   string fullName = getFullName(myUser);
   return fullName + " has completed the task!";
}

private string getFullName(User myUser)
{
    return myUser.FirstName + " " + myUser.LastName;
} 
```

Enter fullscreen mode Exit fullscreen mode

不过，到目前为止，我只是以过程化的方式编写和重构代码。我的下一步是把这个方法放到它应该属于的对象中。在这个简单的例子中，`getFullName()`有一个明显的家。感觉放在`User`类本身更合适。而且，因为它只需要检查它拥有的元素，所以我可以把这个方法变成一个 readonly 属性。`FullName`现在只是`User`实例的另一个属性:

```
public class User
{
    public readonly string FirstName;
    public readonly string LastName;

    public string FullName
    {
        get 
        {
            return FirstName + " " + LastName;
        }
    }

    public User(string firstName, string lastName)
    {
        FirstName = firstName;
        LastName = lastName;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这也美化了我最初使用的方法:

```
public string GetTaskResponse(User myUser)
{ 
   return myUser.FullName + " has completed the task!";
} 
```

Enter fullscreen mode Exit fullscreen mode

对于这样一个简单的例子，我已经经历了足够多次这样的心理舞蹈，可以直接跳到最后的结果。但是，随着围绕特性的逻辑变得越来越混乱和复杂，我会更加谨慎地完成这个舞蹈。重构并不明显。对我来说，面向对象的代码是大量代码编辑的最终结果。

有趣的是，我阅读代码的方式也差不多。一开始，程序代码对我来说更容易理解。如果我在寻找一个特定的逻辑片段，我可能会通过深入过程代码库比在面向对象代码库中跳跃更快地找到它。

如果阅读或编写面向对象的代码对我来说很奇怪，那么为什么要用这种方式编程呢？因为，对我来说，面向对象的设计最终会胜出。应用程序存在和发展的时间越长，OOP 的价值就越大。

一个面向对象的代码库比一个过程化的代码库描绘了一个更易消费的整个应用程序的心智模型。但是，消耗全部自然需要时间。我用 OOP 做的权衡是立即理解一个特定的难题，然后更慢、更有条理地理解整个应用程序。

保持一个干净的、有表现力的、设计良好的面向对象的代码库是一项艰巨的工作。它需要持续的重新评估和纪律。信念的飞跃是，在这个重新评估的中，你巩固了对你实际正在建造的东西的更好理解。