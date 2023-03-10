# 数组和公共语言运行库——一种非常特殊的关系

> 原文：<https://dev.to/mattwarren/arrays-and-the-clr---a-very-special-relationship>

不久前，我写了一篇关于字符串和 CLR 之间存在“特殊关系”的文章，事实证明数组和 CLR 有着更深层次的关系，那种你和 T2 第一次见面就握着手的亲密关系

[![Donald Trump and  Theresa May](img/a54fbcbd20d8391321b98e977e2fc438.png)T2】](http://www.telegraph.co.uk/news/2017/01/27/theresa-may-donald-trump-prove-opposites-can-attract-uk-us-leaders/)

* * *

顺便说一句，如果你喜欢阅读关于 **CLR 内部**的文章，你可能会觉得这些文章很有趣:

*   [CLR 线程池的‘线程注入’算法](http://mattwarren.org/2017/04/13/The-CLR-Thread-Pool-Thread-Injection-Algorithm/?devto-recommended=1)
*   [CLR 在执行一行代码之前要做的 68 件事](http://mattwarren.org/2017/02/07/The-68-things-the-CLR-does-before-executing-a-single-line-of-your-code/?devto-recommended=1)
*   [怎么做。网络代表工作？](http://mattwarren.org/2017/01/25/How-do-.NET-delegates-work/?devto-recommended=1)
*   [为什么反射慢？](http://mattwarren.org/2016/12/14/Why-is-Reflection-slow/?devto-recommended=1)
*   [如何使用“固定”关键字？](http://mattwarren.org/2016/10/26/How-does-the-fixed-keyword-work/?devto-recommended=1)

* * *

## 公共语言运行库(CLR)的基础

数组是 CLR 的一个基本组成部分，它们被包含在 [ECMA 规范](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/dotnet-standards.md)中，以表明*运行时*必须实现它们:

[![Single-Dimensions Arrays (Vectors) in the ECMA Spec](img/61b61e6cb8aa033d4aa3a4d6964dcb80.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--tic1ys-K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://mattwarren.oimg/2017/05/Single-Dimensions%2520Arrays%2520%28Vectors%29%2520in%2520the%2520ECMA%2520Spec.png)

此外，还有几个专门处理数组的 IL(中间语言)指令:

*   newarr <etype>*   用 etype 类型的元素创建一个新数组。</etype> 
*   元素. ref
    *   将索引处的元素作为 O 加载到堆栈的顶部。O 的类型与推送到 CIL 堆栈上的数组的元素类型相同。
*   " T0 "的发音
    *   用堆栈上的值替换索引处的数组元素(还有`stelem.i`、`stelem.i1`、`stelem.i2`、`stelem.r4`等)
*   ldlen
    *   将数组的长度(类型为 native unsigned int)压入堆栈。

这是有意义的，因为数组是许多其他数据类型的构建块，您希望它们在现代高级语言(如 C#)中可用、定义良好且高效。没有数组，你就没有列表、字典、队列、栈、树等等，它们都是建立在数组之上的，数组以一种类型安全的方式提供了对连续内存块的低级访问。

### 内存和类型安全

这个*内存*和*类型安全*很重要，因为没有它。NET 不能被描述为“托管运行时”,当你用一种更低级的语言编写代码时，你将不得不处理你所遇到的各种问题。

更具体地说，当您使用数组时，CLR 提供了以下保护(来自 BOTR“CLR 简介”页面中关于[内存和类型安全](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/intro-to-clr.md#memory-and-type-safety)的部分):

> 虽然 GC 对于确保内存安全是必要的，但这还不够。GC 不会阻止程序**索引数组**的结尾或者访问对象结尾的字段(如果使用基址和偏移量计算来计算字段的地址，这是可能的)。**然而，如果我们确实防止了这些情况，那么我们确实可以让程序员无法创建内存不安全的程序**。
> 
> 虽然通用中间语言(CIL)确实有可以获取和设置任意内存的运算符(因此违反了内存安全)，但它也有跟在内存安全运算符后面的**，CLR 强烈鼓励在大多数编程中使用它们:**
> 
> 1.  字段提取运算符(LDFLD、STFLD、LDFLDA ),通过名称提取(读取)、设置和获取字段的地址。
> 2.  **数组提取操作符(LDELEM，STELEM，LDELEMA)** 通过索引提取、设置和获取数组元素的地址。**所有数组都包含一个指定其长度的标签**。这有助于在每次访问之前进行自动边界检查。

此外，在同一个 BOTR 页面中的[可验证的代码强制内存和类型安全](https://github.com/dotnet/coreclr/blob/master/Documentation/botr/intro-to-clr.md#verifiable-code---enforcing-memory-and-type-safety)部分

> 在实践中，所需的运行时检查次数实际上非常少。它们包括以下操作:
> 
> 1.  将指向基类型的指针强制转换为指向派生类型的指针(可以静态检查相反的方向)
> 2.  **数组边界检查**(正如我们看到的内存安全)
> 3.  将指针数组**中的元素赋给新的(指针)值**。这个特殊的检查是必需的，因为 **CLR 数组有自由的转换规则**(稍后会有更多内容...)

然而，您不能免费获得这种保护，这是有代价的:

> 注意，进行这些检查的需要对运行时提出了要求。特别是:
> 
> 1.  GC 堆中的所有内存都必须用其类型进行标记(这样就可以实现转换操作符)。这种类型信息必须在运行时可用，并且必须足够丰富以确定强制转换是否有效(例如，运行时需要知道继承层次)。事实上，GC 堆上每个对象的第一个字段都指向表示其类型的运行时数据结构。
> 2.  **所有数组也必须有它们的大小**(用于边界检查)。
> 3.  **数组必须有关于其元素类型的完整类型信息**。

* * *

## 实现细节

事实证明，数组的大部分内部实现最好被描述为*魔法*，马克·格拉维尔的这个堆栈溢出[评论很好地总结了这一点](http://stackoverflow.com/questions/19914523/mystery-behind-system-array#comment29631862_19914523)

> 数组基本上是巫术。因为它们先于泛型，但必须允许动态类型创建(即使在。NET 1.0)，它们是使用技巧、黑客和花招实现的。

是的，没错，在泛型出现之前，数组就已经参数化了。这意味着你可以在编写`List<int>`或`List<string>`之前很久就创建像`int[]`和`string[]`这样的数组，这只有在。NET 2.0。

### 特殊助手类

所有这些*魔法*或*戏法*都是由两件事促成的:

*   CLR 打破了所有常见的类型安全规则
*   名为`SZArrayHelper`的特殊数组助手类

但首先是为什么，为什么需要所有这些技巧？从[开始。NET 数组，IList < T >，泛型算法，STL 呢？](https://blogs.msdn.microsoft.com/bclteam/2004/11/19/net-arrays-ilistt-generic-algorithms-and-what-about-stl-brian-grunkemeyer/):

> 当我们设计通用集合类时，困扰我的一件事是如何编写一个通用算法，既能在数组上工作，又能在集合上工作。为了推动泛型编程，我们当然必须使数组和泛型集合尽可能无缝。它认为应该有一个简单的方法来解决这个问题**这意味着你不应该写两次相同的代码，一次用 IList < T >，一次用 T[]** 。我想到的解决方案是数组需要实现我们的通用 IList。我们让 V1 的数组实现了非泛型的 IList，这非常简单，因为 IList 缺乏强类型，而且我们的基类适用于所有数组(System。数组)。**我们需要的是以强类型的方式为 IList<T>T3】做同样的事情。**

但这仅适用于常见情况，即“一维”阵列:

> 不过这里有一些限制—**我们不想支持多维数组，因为 IList < T >只提供一维访问**。此外，具有非零下限的数组相当奇怪，可能不太适合 IList < T >，因为大多数人可能会从 0 开始迭代，直到返回 IList 的 Count 属性。于是，**改为制定制度。数组实现 IList < T >，我们做了 T[]实现 IList<T>T3】。这里 T[]表示一个以 0 为下界的一维数组(内部通常称为 SZArray，但我认为 Brad 想在某个时间点公开推广' vector '这个术语)，元素类型为 T，所以 Int32[]实现 IList < Int32 >，String[]实现 IList < String >。**

此外，来自[数组源代码](https://github.com/dotnet/coreclr/blob/32f0f9721afb584b4a14d69135bea7ddc129f755/src/vm/array.cpp#L1369-L1428)的注释进一步阐明了原因:

```
//----------------------------------------------------------------------------------
// Calls to (IList<T>)(array).Meth are actually implemented by SZArrayHelper.Meth<T>
// This workaround exists for two reasons:
//
//    - For working set reasons, we don't want insert these methods in the array 
//      hierachy in the normal way.
//    - For platform and devtime reasons, we still want to use the C# compiler to 
//      generate the method bodies.
//
// (Though it's questionable whether any devtime was saved.)
//
// ....
//---------------------------------------------------------------------------------- 
```

所以这样做是为了*方便*和*高效*，因为他们不想让`System.Array`的每个实例都携带着`IEnumerable<T>`和`IList<T>`实现的所有代码。

这种映射是通过调用[getactualimplementationforarraygenericistorireadonlylistmethod(..)](https://github.com/dotnet/coreclr/blob/a9b25d4aa22a1f4ad5f323f6c826e318f5a720fe/src/vm/methodtable.cpp#L6870-L6873)，获得 CoreCLR 源码中最佳方法名奖！！它负责从 [SZArrayHelper](https://github.com/dotnet/coreclr/blob/68f72dd2587c3365a9fe74d1991f93612c3bc62a/src/mscorlib/src/System/Array.cs#L2595-L2778) 类中连接相应的方法，即`IList<T>.Count` - > `SZArrayHelper.Count<T>`或者如果该方法是`IEnumerator<T>`接口的一部分，则使用[SZGenericArrayEnumerator<T>](https://github.com/dotnet/coreclr/blob/68f72dd2587c3365a9fe74d1991f93612c3bc62a/src/mscorlib/src/System/Array.cs#L2718-L2776)。

但是这有可能导致安全漏洞，因为它破坏了正常的 C#类型系统保证，特别是关于`this`指针。为了说明这个问题，下面是 [`Count`属性](https://github.com/dotnet/coreclr/blob/68f72dd2587c3365a9fe74d1991f93612c3bc62a/src/mscorlib/src/System/Array.cs#L2627-L2633)的源代码，注意对`JitHelpers.UnsafeCast<T[]>`的调用:

```
internal int get_Count<T>()
{
    //! Warning: "this" is an array, not an SZArrayHelper. See comments above
    //! or you may introduce a security hole!
    T[] _this = JitHelpers.UnsafeCast<T[]>(this);
    return _this.Length;
} 
```

哎呀，它必须重新映射`this`才能在正确的对象上调用`Length`！！

以防这些评论还不够，在课程的顶部有一个措辞非常强硬的评论[，进一步阐述了风险！！](https://github.com/dotnet/coreclr/blob/68f72dd2587c3365a9fe74d1991f93612c3bc62a/src/mscorlib/src/System/Array.cs#L2572-L2594)

通常所有这些魔法对你来说都是隐藏的，但偶尔也会泄露出来。例如，如果运行下面的代码，`SZArrayHelper`将出现在`NotSupportedException`属性的`StackTrace`和`TargetSite`中:

```
try {
    int[] someInts = { 1, 2, 3, 4 };
    IList<int> collection = someInts;
    // Throws NotSupportedException 'Collection is read-only'
    collection.Clear();         
} catch (NotSupportedException nsEx) {              
    Console.WriteLine("{0} - {1}", nsEx.TargetSite.DeclaringType, nsEx.TargetSite);
    Console.WriteLine(nsEx.StackTrace);
} 
```

### 去除界限检查

运行库还以更传统的方式提供对数组的支持，第一种方式与性能有关。当提供*内存安全*时，数组边界检查是很好的，但是它们是有代价的，所以在可能的情况下，JIT 删除任何它知道是多余的检查。

它通过计算一个`for`循环访问的值的*范围*,并将这些值与数组的实际长度进行比较。如果它确定*永远不会*试图访问数组允许边界之外的项，那么运行时检查就会被删除。

有关更多信息，下面的链接将带您到 JIT 源代码中处理这一问题的区域:

*   [JIT 试图删除范围检查](https://github.com/dotnet/coreclr/blob/ec80b02b61839af453ce297faf4ce074edeee9da/src/jit/compiler.cpp#L4524-L4525)
*   [range check::optimizer ange check(..)](https://github.com/dotnet/coreclr/blob/27b2300f790793733e501497203316ccad390e2b/src/jit/rangecheck.cpp#L201-L303)
    *   依次调用 [RangeCheck::GetRange(..)](https://github.com/dotnet/coreclr/blob/27b2300f790793733e501497203316ccad390e2b/src/jit/rangecheck.cpp#L1261-L1290)
    *   还要调用[编译器::optRemoveRangeCheck(..)](https://github.com/dotnet/coreclr/blob/c06fb332e7bb77a55bda724a56b33d6094a0a042/src/jit/optimizer.cpp#L7255-L7322)实际清除量程-检查
*   非常有用的源代码注释[解释了范围检查移除逻辑](https://github.com/dotnet/coreclr/blob/master/src/jit/rangecheck.h#L5-L58)

如果你真的很感兴趣，看看我整理的这个要点，探索边界检查被“移除”和“不被移除”的场景。

### 分配一个数组

运行库帮助完成的另一项任务是分配数组，使用手写的汇编代码，以便尽可能优化方法，请参见:

*   [JIT _ trilalo loc::genallcarray(-什么)](https://github.com/dotnet/coreclr/blob/0ec02d7375a1aa96206fd755b02e553e075ac3ae/src/vm/i386/jitinterfacex86.cpp#L885-L1109)
*   [在汇编代码中打补丁](https://github.com/dotnet/coreclr/blob/0ec02d7375a1aa96206fd755b02e553e075ac3ae/src/vm/i386/jitinterfacex86.cpp#L1082-L1104)

### 运行时不同地对待数组

最后，因为数组与 CLR 是如此紧密地交织在一起，所以在很多地方它们都被当作一种特殊情况来处理。例如[在 CoreCLR 源代码中搜索‘is array()’](https://github.com/dotnet/coreclr/search?l=C%2B%2B&q=path%3A%2Fsrc+IsArray%28%29&type=&utf8=%E2%9C%93)会返回超过 60 个结果，包括:

*   数组的方法表是以不同的方式构建的
    *   [method table builder::BuildInteropVTableForArray(..)](https://github.com/dotnet/coreclr/blob/a9b25d4aa22a1f4ad5f323f6c826e318f5a720fe/src/vm/classcompat.cpp#L543-L608)
*   当你在一个数组上调用`ToString()`时，你会得到特殊的格式，比如 System。Int32[]'或' MyClass[，]'
    *   [TypeString::AppendType(..)](https://github.com/dotnet/coreclr/blob/32f0f9721afb584b4a14d69135bea7ddc129f755/src/vm/typestring.cpp#L903-L937)

* * *

所以，可以说数组和 CLR 有一种非常特殊的关系

* * *

## 进一步阅读

一如既往，这里有更多的链接供您欣赏！！

*   [CSharp 数组规范](https://github.com/ljw1004/csharpspec/blob/gh-pages/arrays.md)
*   [。NET 类型内部——从微软 CLR 的角度看——数组](https://www.codeproject.com/Articles/20481/NET-Type-Internals-From-a-Microsoft-CLR-Perspecti?fid=459323&fr=26#20)
*   [CLR INSIDE OUT -调查内存问题](http://web.archive.org/web/20081203124917/http://msdn.microsoft.com/msdnmag/issues/06/11/CLRInsideOut/)
*   [数组内部](http://www.abhisheksur.com/2011/06/internals-of-array.html)
*   的内部结构。NET 对象和 SOS 的使用
*   [内存布局。网络阵列](https://windowsdebugging.wordpress.com/2012/04/07/memorylayoutofarrays/)
*   [内存布局。网络阵列(x64)](https://windowsdebugging.wordpress.com/2012/04/24/memorylayoutofarraysx64/)
*   [为什么多维数组在。比普通阵列慢？](http://stackoverflow.com/questions/468832/why-are-multi-dimensional-arrays-in-net-slower-than-normal-arrays)
*   [C #中数组如何部分实现 IList < T >？](http://stackoverflow.com/questions/11163297/how-do-arrays-in-c-sharp-partially-implement-ilistt/11164210#11164210)
*   [TypeDependencyAttribute(" System。SZArrayHelper”)为 IList < T >，IEnumerable < T >和 ICollection < T >？](http://stackoverflow.com/questions/33632073/purpose-of-typedependencyattributesystem-szarrayhelper-for-ilistt-ienumer/33632407#33632407)
*   [yield return 返回哪种类](http://stackoverflow.com/questions/15341882/what-kind-of-class-does-yield-return-return/15341925#15341925)
*   在共享资源 CLI (SSCLI)中实现的 SZArrayHelper

### 数组引用源代码

*   [Array.cs](https://github.com/dotnet/coreclr/blob/master/src/mscorlib/src/System/Array.cs)
*   [array.cpp](https://github.com/dotnet/coreclr/blob/master/src/vm/array.cpp)
*   [array.h](https://github.com/dotnet/coreclr/blob/master/src/vm/array.h)

* * *

数组和公共语言运行时——一种非常特殊的关系这篇文章首先出现在我的博客【mattwarren.org T2】上