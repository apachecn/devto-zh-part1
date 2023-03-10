# 具有接口的灵活架构

> 原文：<https://dev.to/funkysi1701/flexible-architecture-with-interfaces-25k1>

我写过几次关于[接口](https://dev.to/funkysi1701/interfaces-34on-temp-slug-2228381)的博客，以及它们对于产生高质量的可维护代码是多么有用。让我们来看一个问题和我引以为豪的解决方案。

如前所述，我正在将[图像](https://dev.to/funkysi1701/moving-files-into-blob-storage-1ckh-temp-slug-2044961)从 AWS 转移到 Azure blob 存储。现在实际的文件本身已经被移动了，我需要修改引用它们的代码。

现在，我可以找到所有使用 AWS API 的代码，并将其替换为 Azure API，但我不太擅长预测未来，我们可能会在 Azure 上停留一段时间，我们可能会转向 AWS 或谷歌云，或者我们可能会回到服务器上的文件。

让我们试着编码一个尽可能灵活的解决方案。你可能已经猜到了，我将创建一个接口。

起初我想创建一个名为 **ICloudStorage** 的接口，但是这不够灵活，因为如果我们回到在服务器上粘贴文件会发生什么，所以我创建了 **IStorage** 。

我创建了三个实现 IStorage 的类， **AWSStorage** ， **AzureStorage** ，主要用于测试当前的**文件存储**。然后我创建了一个调用这三个类的类存储。最初我是这样创建的

```
public class Storage
{
  private IStorage _repo;
  public Storage(IStorage repo)
  {
    _repo = repo;
  }
} 
```

然而，这需要我称之为 like Storage(new AzureStorage())，并且我需要知道我的代码中的任何地方我想要使用哪个实现。这并不太糟糕，因为当我们从 AWS 改为 Azure 时，我们需要在整个代码中进行查找和替换，并替换所有 AWSStorage，使它们成为 AzureStorage。

然而，我们可以做得更好。

```
public class Storage
{
  private IStorage _repo;
  public Storage()
  {
    Type obj = Type.GetType(ConfigurationManager.AppSettings["DefaultStorageRepository"]);
    ConstructorInfo constructor = obj.GetConstructor(new Type[] { });
    _repo = (IStorage)constructor.Invoke(null);
  }
} 
```

这段代码将从 web.config 中读取要使用的实现，并决定调用哪个类。这意味着要从 AWS 转换到 Azure，我们不需要重新部署任何代码，我们需要做的只是更改 web.config。

让我们看看这三行，看看我们是否能理解正在发生的事情。

**类型。GetType()** 直接从 web.config 中获取类型

目标。GetConstructor() 这将获取我们刚刚找到的类型的构造函数。

**构造器。Invoke** 然后调用构造函数，然后转换成接口，这样就可以被 _repo 变量使用。

这一切都相当简单和有意义，但是它产生了一些非常灵活的代码，并允许代码无需重新编译即可扩展。

让我们看一个假设的例子。我们想增加对谷歌云存储的支持。我们需要做的就是创建一个实现 IStorage 接口的类库，将编译后的二进制文件放在网站中，并更新 web.config 以引用它。我没有试过这个假设的例子，所以它可能比我想象的更复杂，但理论上它应该是可行的。

我对这段代码的灵活性感到非常兴奋，希望我能更经常地使用这样的代码，因为我理解了它。

帖子[带接口的灵活架构](https://www.funkysi1701.com/2018/01/29/flexible-architecture/)最早出现在 [Funky Si 的技术演讲](https://www.funkysi1701.com)上。