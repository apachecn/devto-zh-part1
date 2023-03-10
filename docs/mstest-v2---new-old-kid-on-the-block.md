# MSTest v2 -新(旧)的孩子

> 原文：<https://dev.to/franndotexe/mstest-v2---new-old-kid-on-the-block>

MSTest 已经很久没有讨论现代测试框架了。然而，现在是 2017 年，MSTest 又一次获得了主动更新，部分原因是它是开源的。让我们来看看这些令人兴奋的新功能是什么。

请注意，这篇文章将涵盖 MSTest v2 NuGet 包目前在`pre-release`中的特性。

**注意**:记住在您将要使用的 NuGet 包中使用`pre-release`标志:

*   MSTest。框架
*   MSTest。测试适配器

也推荐 visual Studio 2015+；您的里程可能会因 2013 年而异。

这个测试版不是 MSTest v2 的第一个公共迭代，但是包含了下面讨论的一些特性。对参数化测试用例有更强的支持是非常重要的，这些特性在 1.2.0 测试版中已经有了；版本 1.1.13 是 github 的第一个 v2 [版本。](https://github.com/Microsoft/testfx-docs/blob/master/docs/releases.md#1113)

每个版本的发布说明可以在[这里](https://github.com/Microsoft/testfx/releases)找到。

在一篇关于 MSTest v2 和 mstest.exe 的未来的帖子上，用户(微软假定的雇员)“Abhitej_MSFT”澄清道:

> > "mstest.exe" does not support MSTest V2 test. In the TFS build template, the test runner should be "Visual Studio Test Runner" ( [https://msdn.microsoft.com/en-us/library/ms253138 (v = vs.110). aspx#Runner](https://msdn.microsoft.com/en-us/library/ms253138(v=vs.110).aspx#Runner) 。 I hope your definition doesn't require legacy test settings. If you encounter any problems, please let us know through aajohn@microsoft.com [.](mailto:aajohn@microsoft.com)

来源-[https://blogs . msdn . Microsoft . com/devo PS/2017/02/25/ms test-v2-now-and-ahead/# comment-78796](https://blogs.msdn.microsoft.com/devops/2017/02/25/mstest-v2-now-and-ahead/#comment-78796)

我还没有遇到过 NUnit 已经很久没有的 MSTest v2 特性。然而，如果您决定在另一个测试框架上使用 MSTest，这里讨论的特性仍然非常有用。

对于那些想了解全部内容的人来说，可以在 [github](https://github.com/Microsoft/testfx) 上找到 MSTest 的存储库。

## 特性

### 【dynamic CDATA】[【141】](https://github.com/Microsoft/testfx/issues/141)

参数化测试在 MSTest 中早就有了，使用的`DataRowAttribute`看起来像这样:

```
[TestMethod]
[DataRow (1, 2, 3)]
[DataRow (4, 5, 6)]
public void MyParameterizedTest (int a, int b, int c) {
    //perform assertions on a,b and c
} 
```

Enter fullscreen mode Exit fullscreen mode

产生的两个测试都使用数据“行”的一个实例。然而，如果我们想要在测试中重用这些相同的值呢？因为在使用这些值的每个测试中都重复这些值是低效的，所以 v2 提供了`DynamicDataAttribute` :

```
private static IEnumerable<object[]> ReusableTestData =>
    new List<object[]> {
        new object[] { 1, 2, 3 },
        new object[] { 4, 5, 6 }
    };

[TestMethod]
[DynamicData (nameof (ReusableTestData))]
public void MyParameterizedTest (int a, int b, int c) {
    //perform the same assertions the same as before.
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，这个测试数据可以用于任何数量的测试，而不必复制实际数据，从而使过程更加清晰。熟悉 NUnit 的人可能知道这个叫做`TestCaseSourceAttribute`。

方法也可以用作测试数据。为此，只需使用接受一个`DynamicDataSourceType.Method` `enum`的`DynamicDataAttribute`构造函数的重载。默认情况下，框架会假设传入的动态数据的名称是一个`Property`。

* * *

### 参数化测试的自定义测试数据 [#141](https://github.com/Microsoft/testfx/issues/141)

如果测试中使用的参数稍微复杂一点，那么更进一步是有用的。使用`CustomTestDataSourceAttribute`,您现在可以创建一个属性，加载这些数据供您在测试中使用，同时保持您的测试整洁。下面是最终结果:

```
[TestMethod]
[CarTestData]
public void ATestUsingACar (string model, int year) {
    var carUnderTest = new Car { Model = model, Year = year };
    //perform assertion on the car object.
}

//config for the 'CarTestData' attribute:
class CarTestDataAttribute : Attribute, ITestDataSource {
    public IEnumerable<object[]> GetData (MethodInfo methodInfo) {
        return new List<object[]> {
            new object[] { "Ford", 1990 },
            new object[] { "Nisson", 2017 }
        };
    }

    public string GetDisplayName (MethodInfo methodInfo, object[] data) {
        if (data != null) {
            return string.Format (CultureInfo.CurrentCulture, "{0} ({1})", methodInfo.Name, string.Join (",", data));
        }
        return null;
    }
}

public class Car {
    public string Model { get; set; }
    public int Year { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

创建一个实现 MSTest v2 `ITestDataSource`接口的新`Attribute`允许我们在测试之外设置测试对象，为参数化测试提供可变数量的测试对象，并在其他测试中重用它们，而无需重复。

这在某些情况下可能非常强大，但是我并不完全喜欢用`ITestDataSource.GetData()`返回一个`IEnumerable<object[]>`而不是返回一个`IEnumerable<object>`、`IEnumerable<T>`或提供其他接口。这使得使用这种机制返回非原语有些麻烦。

当通过目标运行程序执行测试时，将显示`ITestDataSource.GetDisplayName`(如果在 Visual Studio 中运行，则显示 VSTest ),当测试被参数化时，允许我们确定测试失败的情况。

* * *

### 断言。那个 [#116](https://github.com/Microsoft/testfx/issues/116)

随着为断言逻辑引入了一个集中的扩展点，MSTest v2 现在提供了`That`，这是一个在`Assert`上的静态属性，它返回实例并允许为扩展断言方法提供一个简单的跳转点。

这里有一个例子:

```
public static class MyAssertExtensions {
    public static void CountIsGreaterThan<T> (this Assert assert, IEnumerable<T> objs, int num) {
        int actualCount = objs.Count ();
        if (actualCount > num) {
            return;
        }
        throw new AssertFailedException ($"Expected {nameof(objs)} count to be greater than {num}, but was {actualCount}.");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这意味着测试在其断言中更具可读性

```
[TestClass]
public class MyTests {
    [TestMethod]
    public void ATest () {
        var aList = new List<int> { 1, 2, 3, 4 };
        Assert.That.CountIsGreaterThan (aList, 0);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`Assert.That`可用于替换和/或补充对`Assert.AreEqual()`和`Assert.IsTrue()`的呼叫。就其本身而言，这些是如此宽泛的调用，以至于在调试或阅读测试时经常会导致混乱。根据我的经验，`Assert.IsTrue()`尤其如此，因为它默认输出`Assert.IsTrue failed`。`AreEquals()`试图通过报告实际值和期望值来减少混淆。

注意:如果您不打算使用扩展方法，帮助解决这个问题的一个快速方法是在传递失败消息时传递被测属性/方法的名称。
一个例子

```
public void MyTest () {
    var aList = new List<int> { 1, 2, 3 };

    //nameof() is a C#6 feature, but is not required to make this work.
    Assert.IsTrue (aList.Count > 3, $"{nameof(aList.Count)}");

    //failure prints: Assert.IsTrue failed. Count
} 
```

Enter fullscreen mode Exit fullscreen mode

用这种方法增强`Assert.IsTrue`的一个例子是:

```
public static class MyAssertExtensions {
    public static void IsTrue<T> (this Assert assert, T instance, Expression<Func<T, bool>> assertionExpression) {
        if (assertionExpression.Compile ().Invoke (instance)) { return; }
        throw new AssertFailedException ($"Assertion failed for expression {assertionExpression}");
    }
}

//Example
public void IsTrue_Extended_Test () {
    var aList = new List<int> { 1, 2, 3, 4 };
    Assert.That.IsTrue (aList, list => list.Count > 4);

    //failure prints: Assertion failed for expression 'a => (a.Count > 4)'.
} 
```

Enter fullscreen mode Exit fullscreen mode

更进一步，我们可以创建一个流畅的语法，使其在未来具有更大的可扩展性:

```
[TestMethod]
public void IsGreaterThan_Redux () {
    var aList = new List<int> { 1, 2, 3, 4 };
    Assert.That.For(aList).IsTrue (list => list.Count > 4);

    //failure output: Assertion failed for expression 'list => list.Count > 4'
}

public static class MyAssertionExtensions {
    public static For<T> For<T> (this Assert assert, T instance) {
        return new For<T> (instance);
    }
}

public class For<T> {
    private readonly T _instanceUnderTest;

    public For (T instanceUnderTest) {
        _instanceUnderTest = instanceUnderTest;
    }

    public void IsTrue (Expression<Func<T, bool>> assertionExpression) {
        if (assertionExpression.Compile ().Invoke (_instanceUnderTest)) { return; }
        throw new AssertFailedException ($"Assertion failed for expression '{assertionExpression}'.");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

显然，用`Assert.That`创建扩展方法大大缓解了可读性问题。关于其他参考点，请参见[原始 RFC](https://github.com/Microsoft/testfx-docs/blob/master/RFCs/002-Framework-Extensibility-Custom-Assertions.md) 。

虽然这不是对 MSTest v2 管道中新特性的全面概述，但我已经介绍了那些让我觉得特别有用的特性。

有关 MSTest v2 的更多信息，请查看以下链接:

*   [储存库](https://github.com/Microsoft/testfx)
*   [文档](https://github.com/Microsoft/testfx-docs)
*   [针对 v2 的初始微软博客帖子](https://blogs.msdn.microsoft.com/devops/2016/06/17/taking-the-mstest-framework-forward-with-mstest-v2/)

MSTest v2 无疑使 MSTest 成为一个更相关的测试框架。这里的概念是我作为 NUnit 的当前用户最感兴趣的方面。虽然我自己还没有从 NUnit 转换到 MSTest v2，但这些新功能使得考虑这种可能性更加有利。与其他测试框架相比，希望这些特性与未来的更多特性相结合，继续改善开发人员对 MSTest 的看法。