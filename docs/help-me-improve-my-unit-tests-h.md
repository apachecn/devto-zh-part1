# 帮助我改进我的单元测试

> 原文：<https://dev.to/meanin/help-me-improve-my-unit-tests-h>

我是。net 开发者。我已经每天使用 TDD 三年了。时不时地，我会发现一些新的东西。一些可能提高我的技能，甚至给我的雇主带来好处的事情。我也是一个流利的语法爱好者，我使用 [Moq](https://github.com/Moq/moq4/wiki/Quickstart) 创建测试替身，使用[fluent assessions](http://fluentassertions.com/)创建测试断言。

# 测试布局

我喜欢在我的测试方法中包含 AAA (Arrange，Act，Assert)后面的注释。这让我想到什么是必要的安排部分，什么是行动和断言部分。我总是试图从 assert 中提取尽可能多的内容，并将其放入 arrange 部分。在下面的例子中，有测试方法的输入值和提取的预期输出。

```
 [Fact]
        public void GetValue_WhenGivenNumberIs3_ShouldReturnFizz()
        {
            // arrange

            var fizzBuzz = new FizzBuzz.FizzBuzz();
            const int number = 3;
            const string expected = "Fizz";

            // act

            var result = fizzBuzz.GetValue(number);

            // assert

            result.Should().BeEquivalentTo(expected);
        } 
```

Enter fullscreen mode Exit fullscreen mode

这种提取的好处是一个非常好的组合断言部分。在我们的版本控制系统中，我们不会为每个字母付费，所以对我来说，这看起来像是一种默认的方法。

你的测试中也包括这种评论吗？

# 测试库

创建测试库我用后缀`.Tests`命名单元测试，用后缀`.IntegrationTests`命名集成测试。库名的第一部分是被测库的名称。我通常为单个源代码类创建测试类(文件)。很少，当被测试的类足够大，或者有如此多的案例场景时，我把它分成几个文件，每个文件用于一个方法，或者用于遵循相似场景的一组方法。

你对测试库有什么感受？我错过了什么吗？

# 命名约定

最近，我和我的同事讨论了测试方法命名约定。我们争论在测试方法名中保留还是保留单词`"Should"`。这次谈话给我留下了深刻的印象，在下班后的[晚上](https://dev.to/meanin/how-to-deal-with-evenings-bursts-of-creativity-pc)我一直在想这个问题。在我们公司，我们遵循这个[条款](https://dzone.com/articles/7-popular-unit-test-naming)的惯例，但我个人喜欢用`When`和`Should`来扩展它。仔细看看它:

*   `{MethodName}_{StateUnderTest}_{ExpectedBehavior}`

```
public void GetValue_3_Fizz() 
```

Enter fullscreen mode Exit fullscreen mode

*   `{MethodName}_When{StateUnderTest}_Should{ExpectedBehavior}`

```
public void GetValue_WhenGivenNumberIs3_ShouldReturnFizz() 
```

Enter fullscreen mode Exit fullscreen mode

使用这种方法，测试方法的名称看起来像一个完整的句子。即使对于非常年轻的开发人员来说，这也使得它们一目了然。老实说，对我来说，接下来的测试甚至可以被视为某种用例的文档。

你怎么想？我还应该用 when/should 吗？坚持会好吗？

还有一个问题——我准备好教年轻同事测试方法了吗？