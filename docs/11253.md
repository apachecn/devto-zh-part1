# 快速提示:当用 Moq 测试时，尝试 Lambdas 进行更灵活的测试

> 原文：<https://dev.to/seankilleen/quick-tip-when-testing-with-moq-try-lambdas-for-more-flexible-tests-1k9o>

模拟对于测试艺术来说是无价的。对于那些不熟悉的人来说，模拟本质上是一种伪造组件并让它做你想要它做的事情的方法(例如，总是失败的验证器，或者当给定某个参数时总是返回`1`的方法)。

虽然我对库不太了解 <sup id="fnref:1">[1](#fn:1)</sup> ，但我碰巧对 [Moq](https://github.com/Moq/moq4) 非常熟悉，所以这是我最常用的一个。

最近，我向一位开发人员展示了一个技巧，我突然意识到它可能值得与更广泛的受众分享。

测试的一个大挑战是要保持足够的针对性，使它们能够准确地捕捉到你想要做的事情，同时又要足够灵活，使最小的变化也不会破坏它们。比起可重用性，我更喜欢每个测试的可读性，但是没有人喜欢用一个小的改变来修复 20 个测试。

我看到很多人按照以下方式做事:

```
var mockLogger = new Mock<ILogger>();
var myClass = New MyClass(mockLogger.Object);

mockLogger.Verify(x=> x.Warning("Setting 'mySetting' has no value; using default of 1"); 
```

现在，这一切都很好。但是，如果默认值改变了呢？还是设定的名称？测试太脆弱；很容易失败。

但是，当使用 Moq 时，它允许你用一个 lambda 来指定关于一个字符串的事情*，而不是整个字符串本身:* 

```
mockLogger.Verify(x=> x.Warning(It.Is<string>(str => 
     str.Contains("Setting") && 
     str.Contains("has no value") && 
     str.Contains("using default")))); 
```

这保持了测试的本质，同时加强了它对可能合理变化的东西的抵抗力。它略显冗长，但我不认为这有什么影响。

用不同的方式对待模拟、嘲笑或单元测试？我很想在评论中听到你的意见。

我更喜欢*概念*而不是*图书馆*。如果一个开发人员明白嘲讽的核心是什么，那么库的改变就不应该分阶段进行，尽管他们应该能够表达权衡。↩

[快速提示:当使用 Moq 进行测试时，尝试 Lambdas 进行更灵活的测试](https://SeanKilleen.com/2015/06/Moq-Using-Lambdas/)最初由 Sean Killeen 于 2015 年 6 月 28 日在 SeanKilleen.com[发表](https://SeanKilleen.com)。