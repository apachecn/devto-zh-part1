# 单元测试的商业价值是什么？第一部分

> 原文：<https://dev.to/kritner/what-is-the-business-value-of-unit-testing-part-1-59e5>

[第二部](http://kritner.blogspot.com/2016/02/what-is-business-value-of-unit-testing_22.html)

[第一部](http://kritner.blogspot.com/2016/02/what-is-business-value-of-unit-testing.html)你在这里

最近我们在工作中更加专注于单元测试，出现了一个问题。自动化单元测试提供了什么价值？

教科书(ish)的答案是:如果你用相关的断言对你的代码进行单元测试，有足够好的代码覆盖率，出现错误的可能性就越小。听起来很棒！为什么不是每个人都这么做？单元测试确实需要某种编码风格、松散的依赖关系和一定的计划。这里有一个很好的答案,它深入到单元测试如何/为什么很棒的一些细节——但是我喜欢例子。

需求变化。这是(编程)生活的事实。今天正确的事情，可能几个月或几年后就不正确了。关于单元测试和代码覆盖率的一个很棒的事情是，当一起考虑时，你真的可以感觉到你的代码是否工作正常，甚至可以根据你的单元测试来创建需求！继续这个例子——我们将基于这个需求构建一个超级重要的业务逻辑:

> 作为用户，我需要一种输入数字的方法。一旦输入了号码，我需要把它打印出来给我。

听起来很容易。准备启动一个[新的 github repo](https://github.com/Kritner/UnitTestingBusinessValue/tree/cffd7db6101224062e738414bf10df374769a8cd) 来跟踪进度。

所以根据我们的要求。我将创建一个控制台应用程序，它接受用户的输入，然后将其打印给用户..这大概是宇宙历史上最有用的商业逻辑了。

根据需求，我创建了一个类和方法:

```
 /// <summary>
    /// This class is used to return a number.
    /// </summary>
    public class NumberReturner
    {

        /// <summary>
        /// Return the provided number as a string
        /// </summary>
        /// <param name="numberToReturn">The number to return</param>
        /// <returns>The number as string</returns>
        public string ReturnNumberAsString(int numberToReturn)
        {
            return numberToReturn.ToString();
        }

    } 
```

上面的方法非常容易测试，因为只有一个分支。用一个完全相关的断言，获得 100%的代码覆盖率应该没有问题。

```
 /// <summary>
    /// Tests for NumberReturner
    /// </summary>
    [TestClass]
    public class NumberReturnerTests
    {

        /// <summary>
        /// Ensure ReturnNumberAsString has appropriate return type
        /// </summary>
        [TestMethod]
        public void NumberReturner\_ReturnNumberAsString\_CorrectReturnTypeIsString()
        {
            // Arrange
            int expected = 42;
            NumberReturner biz = new NumberReturner();

            // Act
            var results = biz.ReturnNumberAsString(expected);

            // Assert
            Assert.IsInstanceOfType(results, typeof(string));
        }

        /// <summary>
        /// When ReturnNumberAsString is provided a number, the number is returned as a string
        /// </summary>
        [TestMethod]
        public void NumberReturner\_ReturnNumberAsString\_ReturnsNumberThatWasProvided()
        {
            // Arrange
            int expected = 42;
            NumberReturner biz = new NumberReturner();

            // Act
            var results = biz.ReturnNumberAsString(expected);

            // Assert
            Assert.AreEqual(expected.ToString(), results);
        }

    } 
```

我们的代码覆盖率:

[![](img/b95dd586149c37b6e7e817847aeff9d4.png)T2】](https://1.bp.blogspot.com/-gLidRsOE5tM/VspnHewzGOI/AAAAAAAAC2M/cDNiqFDKESo/s1600/originalCodeCoverage.PNG)

有了上面的测试和代码覆盖，我们可以有把握地说我们已经彻底测试了我们的代码。到目前为止，我们的需求非常简单，但是下一次我们将稍微扩展我们的需求，同时仍然关注我们的单元测试和代码覆盖。以下是截至本帖的回购:[https://github . com/krit ner/UnitTestingBusinessValue/tree/F8 b 21 a5 bde 31635 C2 f 37d 530130 D8 BD 393 eee 23 e](https://github.com/Kritner/UnitTestingBusinessValue/tree/f8b21a5bde31635c2f37d530130d8bd393eee23e)

继续到[第二部分](http://kritner.blogspot.com/2016/02/what-is-business-value-of-unit-testing_22.html)