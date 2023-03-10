# 简化 C#中静态方法的单元测试

> 原文：<https://dev.to/joaofbantunes/simplify-unit-tests-on-static-methods-in-c>

## [T1】简介](#intro)

这篇文章很简单，试图作为单元测试静态方法讨论的开始。

我不是制作静态东西的最大粉丝，主要是因为我在过去被它烧伤过(事实是静态在那些情况下被糟糕地使用，但即使如此，如果我可以，我会避免它)但我明白有时这是做事情的最好方式。

使用静态方法时可能出现的问题之一是如何测试它们(以及测试使用它们的代码，但这是一个更大的问题，我还没有看到很好的解决方案)。

当我们的方法很简单，主要是不使用任何共享状态时，我们很好，我们可以像测试其他方法一样测试它们。但是，当我们在静态方法中使用共享状态时，会出现一个问题:当我们有多个单元测试时，给定的测试框架可能会并行执行它们，我们如何保证一个测试不会与另一个混淆？

我使用的想法(不是我的想法，我在几年前读过，最近看到一个 Twitter 线程在讨论这些问题，我想写这篇文章)是在一个“正常的”非静态类中实现所需的逻辑，我们可以像往常一样进行设计，使用我们自己的 DI。NET 开发人员非常喜欢:P 然后我们实现一个静态类，作为包含逻辑的类的包装，拥有后者的私有实例，并将任何调用转发给它。

## 样品

让我们举一个例子(方法太简单，但它认为这已经足够了):我想在`int`上实现一个扩展方法，返回一个长度等于`int` - `string GetStringWithNLength(this int n)`的`string`。现在由于某种原因，我不想每次都创建一个新的`string`，并且想缓存结果。这是我们的共享状态。

因此，正如我提到的，我将首先创建一个非静态类来保存逻辑。

```
using System;

namespace CodingMilitia.UnitTestingStaticsSample.Library
{
    public class CacheableStuffCalculator
    {
        private readonly ICache<int,string> _cache;

        public CacheableStuffCalculator(ICache<int,string> cache)
        {
            _cache = cache;
        }

        public string GetStringWithNLength(int n)
        {
            return _cache.GetOrAdd(n, nAgain => new string('n', nAgain));
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我们实现静态包装器，它知道如何构造`CacheableStuffCalculator`并提供它的依赖项，然后将任何调用转发给它。

```
namespace CodingMilitia.UnitTestingStaticsSample.Library
{
    public static class StaticCacheableStuffCalculatorWrapper
    {
        private static readonly CacheableStuffCalculator CalculatorInstance;

        static StaticCacheableStuffCalculatorWrapper()
        {
            CalculatorInstance = new CacheableStuffCalculator(new SampleCache<int,string>());
        }

        public static string GetStringWithNLength(this int n)
        {
            return CalculatorInstance.GetStringWithNLength(n);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这个，就很容易像我们通常做的那样在`CacheableStuffCalculator`上进行单元测试，避免共享状态的问题。

```
using System;
using Xunit;
using Moq;

namespace CodingMilitia.UnitTestingStaticsSample.Library.Tests
{
    public class GetStringWithNLengthTest
    {
        [Fact]
        public void GivenLength1ReturnsStringWithLength1()
        {
            //prepare
            var cacheMock = new Mock<ICache<int, string>>();

            cacheMock.Setup(cache => cache.GetOrAdd(It.IsAny<int>(), It.IsAny<Func<int, string>>()))
                     .Returns((int key, Func<int, string> valueProvider) => valueProvider(key));

            var calculator = new CacheableStuffCalculator(cacheMock.Object);

            //execute
            var result = calculator.GetStringWithNLength(1);

            //assert
            Assert.Equal(1, result.Length);
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 包装完毕

仅此而已。我认为这非常简单，是一个很好的解决方案。以至于，我认为这是人们做这种事情的通常方式。但是正如我提到的，我最近在 Twitter 上看到一个帖子，讨论如何在这种场景下进行单元测试，于是我想到把这个帖子放上去。

那么，你会怎么做呢？这看起来是一个不错的方法还是你知道更好的方法？分享你的想法！

Cyaz

PS:样本代码[此处](https://github.com/joaofbantunes/UnitTestingStaticsSample)
PS #2:原贴[此处](https://blog.codingmilitia.com/2017/09/14/a-way-to-simplify-unit-tests-on-static-methods/)