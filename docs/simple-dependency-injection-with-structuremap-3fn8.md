# 使用 StructureMap 的简单依赖注入

> 原文：<https://dev.to/adamkdean/simple-dependency-injection-with-structuremap-3fn8>

通常当我想到 IoC/依赖注入。NET，我想到了 [Ninject](http://www.ninject.org/) ，但是今天我一直在看一个替代品: [StructureMap](http://docs.structuremap.net/index.html) 。它声称是“最古老的 IoC/DI 工具。NET development '，从 2004 年开始开发，并且可以通过 NuGet 获得[，包括用于](https://www.nuget.org/packages/StructureMap/) [MVC3](https://www.nuget.org/packages/StructureMap-MVC3/) 和 [MVC4](https://www.nuget.org/packages/StructureMap.MVC4/) 的附加版本。

IoC/DI 的想法是，你可以很容易地改变一个类的依赖关系，而不必通过大量的代码，将所有硬编码的引用从类 X 改为类 y。

我下面举的例子很容易理解。我们有一个存储类，它将为我们保存一些数据。我们有一个定义了两个方法的接口，`StoreData`和`RetrieveData`。然后，我们有一个模拟堆栈(先进后出)的类，并将利用我们的存储类来保存数据。

例如，如果您想从文本文件、数据库或仅仅是内存中读取/写入对象，但不想在中硬编码该功能，这可能会很有用。

让我们从界面开始，这里没有什么令人困惑的。我们会把所有东西装进一个盒子里，让生活变得更简单。

```
interface IStorageProvider
{
    void StoreData(object data);
    object RetrieveData();
} 
```

接下来，我们将实现一个非常简单的类，它只在内存中保存数据:

```
class ExampleStorageProvider : IStorageProvider
{
    private object data;

    public void StoreData(object data)
    {
        this.data = data;
    }

    public object RetrieveData()
    {
        return this.data;
    }
} 
```

现在是 ObjectStack 类。我们可以使用泛型，但让我们保持简单。它将像常规的`Stack<object>`一样弹出和推送对象，但是将使用一个存储类来保存数据。

```
class ObjectStack
{
    private readonly IStorageProvider _storageProvider;

    public ObjectStack(IStorageProvider storageProvider)
    {
        _storageProvider = storageProvider;
    }

    public void Push(object obj)
    {
        object data = _storageProvider.RetrieveData();
        var stack = (data == null) ? new Stack<object>() : (Stack<object>)data;
        stack.Push(obj);
        _storageProvider.StoreData(stack);
    }

    public object Pop()
    {
        object data = _storageProvider.RetrieveData();
        return (data == null) ? null : ((Stack<object>)data).Pop();
    }
} 
```

所以如果我们看上面的代码，你可以看到它在构造函数中使用了一个`IStorageProvider`类的实例。不要被三元运算符吓到，它们只是简单地表示`value = (condition) value if true : value if false`。在 push 方法中，如果不存在堆栈，它将创建一个新的堆栈；在 pop 方法中，如果对象不存在，它将返回 null。

现在，我们可以像这样简单地创建一个 ObjectStack 实例:

```
var stack = new ObjectStack(new ExampleStorageProvider()); 
```

但是我们已经对这种依赖进行了硬编码，我们真的不想这样做。我们希望尽可能保持这些类的松散联系。这就是如何使用 StructureMap 轻松地将 ExampleStorageProvider 映射到 IStorageProvider:

```
ObjectFactory.Initialize(x => {
    x.For<IStorageProvider>().Use<ExampleStorageProvider>();
}); 
```

就是这样！要使用这种依赖注入获得 ObjectClass 的实例，很简单:

```
var stack = ObjectFactory.GetInstance<ObjectStack>(); 
```

下面是使用上述代码产生结果的完整示例:

```
static void Main(string[] args)
{
    ObjectFactory.Initialize(x => {
        x.For<IStorageProvider>().Use<ExampleStorageProvider>();
    });

    var stack = ObjectFactory.GetInstance<ObjectStack>();

    stack.Push("item one");
    stack.Push("item two");
    stack.Push("item three");
    stack.Pop(); // discard "item three"
    string two = (string)stack.Pop();

    Console.WriteLine(two);
    Console.ReadKey();
} 
```

输出:

```
item two 
```

真的就那么简单。为了进一步阅读，看一看[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection)和[控制反转](https://en.wikipedia.org/wiki/Inversion_of_control)。