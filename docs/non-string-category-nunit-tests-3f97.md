# “非字符串”类别 NUnit 测试

> 原文：<https://dev.to/kritner/non-string-category-nunit-tests-3f97>

对遍布代码库的`[Category("MyCategory")]`感到不太“干净”。这让我感觉好了一点，尽管我希望能够很容易地将 category 属性应用于整个程序集。

```
 // Haven't figured out how to apply to assembly correctly, but added it as a flag in my base anyway
    [AttributeUsage(AttributeTargets.Assembly | AttributeTargets.Class | AttributeTargets.Method)]
    public class BaseCategoryAttribute : CategoryAttribute { }

    public class FastIntegrationTestAttribute : BaseCategoryAttribute { }
    public class LongRunningIntegrationTestAttribute : BaseCategoryAttribute { }
    public class UnitTestAttribute : BaseCategoryAttribute { }
    public class CoreTestAttribute : BaseCategoryAttribute { }

    [TestFixture, UnitTest]
    public class SomeClassTests
    {
        // This test has categories UnitTest, CoreTest
        [Test, CoreTest]
        public void ShouldDoSomething()
        {
        }
    } 
```