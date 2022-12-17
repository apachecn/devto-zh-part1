# 两个 getter 属性进入，一个被解释...另一个也是

> 原文：<https://dev.to/jonstodle/two-getter-properties-enter-one-is-explained-and-so-is-the-other-one-too-e4b>

随着 C# 6 的引入，现在有两种类型的自动只获取属性:

```
public List<string> OldAndFamiliar { get; } = new[] { "Old", "and", "familiar" }.ToList();

public List<string> NewHotness => new[] { "New", "hotness" }.ToList(); 
```

Enter fullscreen mode Exit fullscreen mode

它们可能看起来非常相似，在这种情况下，它们也将返回相同的结果。然而，在幕后，他们是不同的。

*C #中的自动属性*是用`get;`和/或`set;` :
定义的

```
public string AutoProperty { get; set; } 
```

Enter fullscreen mode Exit fullscreen mode

编译器将自动生成一个支持字段以及一个 getter 和 setter。类似这样的东西:

```
private List<string> autoProperty;  
public List<string> AutoProperty  
{
    get { return autoProperty; }
    set { autoProperty = value; }
} 
```

Enter fullscreen mode Exit fullscreen mode

用 C#编程时，您可能已经多次编写过类似的内容。

考虑到编译器如何生成自动属性，现在更容易理解两个不同的 getter only 属性是如何工作的。

`OldAndFamiliar`版本将变成类似这样的东西:

```
private List<string> oldAndFamiliar = new[] { "Old", "and", "familiar" }.ToList();  
public List<string> OldAndFamiliar  
{
    get { return oldAndFamiliar ; }
} 
```

Enter fullscreen mode Exit fullscreen mode

而`NewHotness`版本将变成类似这样的东西:

```
public List<string> NewHotness  
{
    get { return new[] { "New", "hotness" }.ToList(); }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，`OldAndFamiliar`只会给`List<string>`赋值一次(到后台字段)。`NewHotness`每次都会返回一个新的`List<string>`。

`NewHotness`版本的行为就像一个方法，但它看起来像一个属性。这些差不多:

```
public List<string> NewHotness => new[] { "New", "hotness" }.ToList();  
public List<string> GetNewHotness() => new[] { "New", "hotness" }.ToList(); 
```

Enter fullscreen mode Exit fullscreen mode

何时使用哪个，取决于您和您的用例。

编码快乐！

*本帖最初发表于[blog.jonstodle.com](https://blog.jonstodle.com/two-getter-properties-enter-one-is-explained-and-so-is-the-other-one-too/)T3】*