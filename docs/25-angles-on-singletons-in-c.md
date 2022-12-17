# C#中单线上的 2.5 个角度

> 原文：<https://dev.to/jonstodle/25-angles-on-singletons-in-c>

这并不是对 C#中不同单例模式的技术优缺点的全面介绍。这只是我对我在代码中使用的三种方法的思考。

如果你想对 C#中的单例模式有一个适当的、彻底的、技术性的、准确的和智能的了解，看看这个。这是由一个懂行的人完成的:Jon Skeet， [C#堆栈溢出之神](http://stackoverflow.com/users/22656/jon-skeet)。

总之。以下是我的三个实现和想法:

```
public static MySingleton Current { get; } = new MySingleton(); 
```

Enter fullscreen mode Exit fullscreen mode

这是我最近用过几次的一种方法。它非常简洁，但是它有一个缺陷，直到它给我带来麻烦时我才想到它；它在第一次访问`MySingleton`类时实例化该类。

如果你在`MySingleton`上唯一的静态属性是`Current`，这不是问题。然而，我想在使用`Current` : `MySingleton.IsAvailable`之前访问该类的另一个静态属性。如果`IsAvailable`是`false`，我不能实例化`MySingleton`，但是当我第一次访问`MySingleton`时，这是自动发生的。

```
public static MySingleton Current { get; }

static MySingleton()  
{
    Current = Current ?? new MySingleton();
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法和上面的方法差不多。它试图将`Current`赋值给自己，除非它为空，在这种情况下，它将其设置为`MySingleton`的新实例。这仍然存在上述问题；`Current`在第一次访问类时被实例化。

一个额外的陷阱是静态构造函数可能在类被使用之前就被调用了。如果 CLR(在计算机上运行代码)决定有一些空闲时间，它可能会开始调用静态构造函数，以防以后会用到它们。这样做是为了防止以后第一次访问该类时出现延迟。

```
private static MySingleton mySingleton;  
 public static MySingleton Current
 {
     get
     {
         if(mySingleton == null) mySingleton = new MySingleton();
         return mySingleton;
     }
 } 
```

Enter fullscreen mode Exit fullscreen mode

你想要的是这个。属性`Current`的惰性实例化，也就是说，在属性本身被使用之前，它不会被实例化。即使你使用了类的其余部分，`Current`也不会实例化一个新的`MySingleton`实例，直到真正需要的时候。

你最终使用哪一个取决于你的需求。有时，如果 CLR 在真正需要之前实例化 singleton 可能是有益的(如果它有一些空闲的 CPU 时间)。然而，在其他时候，惰性属性可能更可取。

明智选择，快乐编码🙂

*本帖最初发表于[blog.jonstodle.com](https://blog.jonstodle.com/2-5-angles-on-singletons-in-c/)T3】*