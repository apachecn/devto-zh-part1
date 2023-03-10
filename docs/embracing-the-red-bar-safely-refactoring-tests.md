# 拥抱红条:安全地重构测试

> 原文：<https://dev.to/corgibytes/embracing-the-red-bar-safely-refactoring-tests>

你曾经重构过你的测试代码吗？如果没有，我希望你能考虑把这作为你正常练习的一部分。测试代码仍然是代码，应该遵守与直接在生产中运行的代码相同的高标准。

重要的是，重构你的测试代码实际上有点冒险。很有可能你可以把一个完全有效的测试变成一个总是通过的测试，不管它覆盖的代码是否正确。让我们探索一种防止这种可能性的技术。

但是在我深入研究本质之前，让我告诉你我在哪里发现了这项技术，以及为什么我觉得它应该成为每个人重构实践的一部分。

## 背景故事

我第一次读到“重构反对红条”是在几年前，当时我订阅了一个由 Michael Feathers 发表的博客。这篇文章[仍然可用](http://butunclebob.com/ArticleS.MichaelFeathers.RefactoringAgainstTheRedBar)，这很好，因为事实证明迈克尔·费哲也不是这个想法的创始人。他在 XP2005 会议上与伊丽莎白·基奥交谈时发现了这一点，他的文章向读者推荐了[她写的一篇关于这种技术](http://sirenian.livejournal.com/21808.html)的文章。

我经常犯这样的错误，因为我曾经读过一些东西，其他人都已经知道了。这项技术也是如此。我参加了敏捷新英格兰在剑桥举办的第一次 mob 编程会议，我参加了一个 Mob，其中一个成员建议重构测试。我提到了这样做使测试无效的风险，并且我们应该针对红色条进行重构以防御它。最初的反应是茫然的凝视和困惑的表情。当我描述了这种技术并通过引导人群展示了它的优点后，人们非常兴奋，一些人问为什么他们以前从未遇到过这种技术。我提到了它的起源，我受到了其他暴徒的鼓励来进一步推广这个想法。

这就引出了这篇文章和我将在 2016 年敏捷 DC 大会上发表的演讲。这也是为什么我重复别人的想法:这是一个非常好的想法，更多的人需要听到它。非常感谢伊丽莎白和迈克尔在这项技术上的早期工作。

## 是什么让 TDD 这么好用？

简单的回答是，当你严格遵循 [TDD 步骤](http://martinfowler.com/bliki/TestDrivenDevelopment.html)时，如果实现中断，你的测试肯定会失败。这是继续重构代码如此安全的基础。

如果这不是非常清楚的话:

TDD 有三个简单的步骤，我们不断重复，直到我们的应用程序做了我们想要它做的一切。

1.  写一个失败的测试
2.  编写尽可能最简单的产品代码,让它通过测试
3.  重构生产代码，使实现更加简洁
4.  我们需要更多的测试吗？如果是，那么从 1 开始，如果不是，那么我们就完成了

我撒谎说只有三步。典型的 TDD 过程描述忽略了重要的一点。经常被忽略的是一个检查步骤，我在上面已经把它列为第四步。

我发现这是一个非常重要的过程。如果你不知道那一步即将到来，你有时会试图违反第二步最重要的条款:*可能最简单的生产代码*。我认为这值得用一个简单的例子来证明。

### TDD 举例:`Calculator.Add()`

我最喜欢用来演示这个概念的形式是使用 TDD 构建一个简单的计算器，并从加法函数开始。尽管我对代码样本使用了`ruby`,但是请记住这种技术并不是特定于语言的。我将有意避免任何对于不熟悉该语言的人来说可能读起来不太好的`ruby`特性。

下面是我们第一次失败的测试的样子:

```
describe 'additon' do
  specify 'adding two numbers that result in 4' do
    calculator = Calculator.new
    result = calculator.add(2, 2)
    expect(result).to eq(4)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

那么，通过这个测试最简单的方法是什么呢？这个怎么样:

```
class Calculator
  def add(left, right)
    return 4
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在这一点上，我经常听到的反应是:“但这并不完整。2+5 是不行的！”这时我提醒人们我上面概述的隐含的第四步:*我们需要更多的测试吗？*听起来答案是响亮的“是”。至少如果我们希望这个计算器能够做更多的事情，而不仅仅是声称任何两个数字相加得到 4。

下面是测试现在可能的样子:

```
describe 'additon' do
  specify 'adding two numbers that result in 4' do
    calculator = Calculator.new
    result = calculator.add(2, 2)
    expect(result).to eq(4)
  end

  specify 'adding two numbers that result in 5' do
    calculator = Calculator.new
    result = calculator.add(2, 3)
    expect(result).to eq(5)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有一个失败的测试，它证实了对最初实现的反对:仅仅返回 4 是不够的。

那么，让*通过新测试*的最简单方法是什么呢？嗯，那就是返回 5。

```
class Calculator
  def add(left, right)
    return 5
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这会让我们的新测试通过。但是我们有一个问题。我们之前的测试现在失败了。

那么最简单的方法是什么让他们都通过呢？那就是实际做工作。

```
class Calculator
  def add(left, right)
    return left + right
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们有两个一起工作的测试来强制我们的实现按照我们想要的方式工作。只有通过这两个测试，我们才能安全地进行重构。

这就是 TDD 如何能够保证我们的测试套件是我们产品代码的完整描述。但是只要我们简单地重构测试代码，这种保证就会失效。

## 为什么盲目重构测试代码很危险？

严格来说，重构永远不会导致行为的改变。那将违反重构的定义: [*改变一个实现而不改变它的行为*](http://refactoring.com) 。但是错误有时会发生，特别是如果你没有一个自动化的重构工具，并且你正在手工应用重构。请记住，你的重构工具不太可能出现错误，但是[也不是不可能](http://jbazuzicode.blogspot.com/2016/05/extract-method-introduces-bug-in-this.html)。

让我们看一个假设的重构场景，我们试图在测试套件中删除一些重复。

我们将从之前完成的测试套件开始:

```
describe 'additon' do
  specify 'adding two numbers that result in 4' do
    calculator = Calculator.new
    result = calculator.add(2, 2)
    expect(result).to eq(4)
  end

  specify 'adding two numbers that result in 5' do
    calculator = Calculator.new
    result = calculator.add(2, 3)
    expect(result).to eq(5)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

让我们重构一下，去掉一些重复:

```
describe 'additon' do
  [
    { left: 2, right: 2, result: 4 },
    { left: 2, right: 3, result: 5 }
  ].each do |example|
    specify "adding two numbers that result in #{example[:result]}" do
      calculator = Calculator.new
      result = calculator.add(example[:left], example[:right])
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

如果我们运行它，它会通过。但是你发现重构过程中的错误了吗？该断言已被意外删除。代码应该是这样的:

```
describe 'additon' do
  [
    { left: 2, right: 2, result: 4 },
    { left: 2, right: 3, result: 5 }
  ].each do |example|
  specify "adding two numbers that result in #{example[:result]}" do
    calculator = Calculator.new
    result = calculator.add(example[:left], example[:right])
    expect(result).to eq(example[:result])
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 当我们重构测试时，我们如何避免破坏测试？

当我们重构我们的产品代码时，是我们的测试套件提供的安全性让我们安全地重构我们的产品代码。那么，当我们需要重构测试代码时，我们如何才能获得同样的安全性呢？要做到这一点，我们必须以一种会导致我们想要重构的测试失败的方式来破坏我们的产品代码。如果他们没有失败，那我们就有麻烦了。要么我们没有正确地破坏生产代码，要么我们的测试一开始就没有成功。如果我们不能强迫我们的测试失败，那么它们对我们没有任何好处，这需要在继续之前解决。

一旦这些测试正确地失败了，我们就可以重构它们。在我们做的每一个微小的重构之后，测试仍然会失败。如果这些测试中的任何一个开始通过，那么我们在重构的某个地方犯了一个错误。这就是上面例子中引入的错误会发生的情况。

在我们完成了重构测试之后，我们可以恢复我们所做的改变来破坏我们的生产代码，这将导致我们所有重构的测试开始通过。如果我们的任何测试仍然失败，那么我们在重构中也犯了一个错误。但是这一次，我们没有创建一个总是通过的测试，而是创建了一个总是失败的测试。

在我们浏览几个例子之前，让我们稍微简化一下工作流程，以便进行回顾。

1.  破坏我们的生产代码，导致我们的测试失败
2.  重构我们的测试代码
3.  确保我们的测试仍然失败
4.  恢复对生产代码的更改
5.  验证测试再次通过

### 一个红色条重构的例子

让我们从我们在上面的 TDD 示例中创建的示例代码开始。

```
describe 'additon' do
  specify 'adding two numbers that result in 4' do
    calculator = Calculator.new
    result = calculator.add(2, 2)
    expect(result).to eq(4)
  end

  specify 'adding two numbers that result in 5' do
    calculator = Calculator.new
    result = calculator.add(2, 3)
    expect(result).to eq(5)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

下面是通过这些测试的产品代码:

```
class Calculator
  def add(left, right)
    return left + right
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

是时候重构我们的测试了。这里有一点重复。这是我所看到的:

*   每个测试都会实例化`Calculator`类
*   每个测试只是用不同的参数调用`add`,并期望不同的结果

让我们在对红条进行重构的同时解决第一个问题。

第一步是通过故意破坏生产代码来迫使我们正在改变的测试失败。应该这样做:

```
class Calculator
  def add(left, right)
    return 0
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们通过运行我们的测试套件并确保我们想要更改的测试失败来验证这一点。如果更多的测试失败也没关系。但是非常重要的是，你打算重构的所有测试都失败了。如果您想要重构的测试没有失败，那么您需要不断地修改您的产品代码，直到它们失败为止。如果你不能让这些测试失败，那么你需要跳到“如果出了问题怎么办？”部分。

现在，我们可以安全地提取`Calculator`类的实例化，它可能看起来像这样:

```
describe 'additon' do
  let(:calculator) { Calculator.new }

  specify 'adding two numbers that result in 4' do
    result = calculator.add(2, 2)
    expect(result).to eq(4)
  end

  specify 'adding two numbers that result in 5' do
    result = calculator.add(2, 3)
    expect(result).to eq(5)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，构建合适的`Calculator`实例的创建逻辑已经被分解到一个地方。

我们需要重新运行我们的测试套件，以确保我们没有犯错误。但是，请记住，我们实际上预计测试会失败。

在这种情况下，我们的测试仍然以我们预期的方式失败。因此，我们现在可以通过恢复生产代码的原始实现来继续进行:

```
class Calculator
  def add(left, right)
    return left + right
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们运行我们的测试套件，以确保一切都通过。

## 出了问题怎么办？

在红条重构过程中，有几个地方你可能会遇到意外。我之前提到过这些，但为了便于参考，我再次重申。

### 我正在更改我的生产代码，但是我不能让我的测试失败！

如果你遇到这个问题，那么你已经有了一个产生假阳性的测试。此时你有几个选择。

1.  *删除测试并将生产代码视为未测试的遗留代码*

一个总是通过的测试和一个总是失败的测试提供了一样多的价值。在最好的情况下，阅读它会让您了解测试作者的原始意图，但它本质上是与您的实现不同步的文档。在这种情况下，删除测试是完全可以接受的选择。没有假阳性测试，您的测试套件不会比以前更弱。既然您已经发现了误报，那么最好继续编写一些代码来覆盖测试想要运行的产品代码。

1.  *回顾变更历史，并尝试恢复测试的工作版本*

如果可以的话，查看一下您的变更控制系统中的测试历史。如果您发现您正在查看提交的测试的第一个版本，那么这个测试*总是*为假阳性，您将需要遵循前面的选项。如果您正在进行的测试有历史记录，那么在您的产品代码仍然被修改以模拟失败的时候，尝试恢复那些更改。如果旧版本的测试失败了，那么您可以恢复您的产品代码修改，看看测试是否通过。如果是这样，那么您已经找到了测试的有效版本。如果那个版本仍然有一些你想做的重构，那么你可以继续并重新开始这个过程。

### 我重构了我的测试，生产代码坏了，但是现在测试通过了！

在这个场景中，您破坏了您的测试。看到这一点的最简单的方法是删除一个关键的断言，否则它会迫使测试失败，或者测试设置过程的运行方式发生了微妙的变化。还原您对测试所做的更改，然后重试。

### 我已经成功地重构了我的测试，并且我已经将我的更改恢复到生产代码，但是现在测试仍然失败！

仔细看看测试失败的原因。我见过这种情况经常发生，因为无效的类型引用或语法错误。因为你是针对红条进行重构的，所以当你重构时，这些东西有时会溜进你的测试中。在这种情况下，修复该错误应该会使您的测试再次通过，但是您会想要重复您的生产代码更改，以再次检查您在尝试再次通过测试时没有意外引入误报。

也有可能您添加了一个断言，即生产代码不能通过测试，或者测试设置与以前不同，并且以前运行的逻辑不再适用。如果是这种情况，那么您需要恢复您对测试所做的更改并重新开始。

## 我们学到了什么？

遵循这个简单的技术是一个很好的方法，可以安全地确保当你重构测试代码时，你的测试套件仍然正确地测试你的生产代码。这是我在任何时候修改测试时都会遵循的一个惯例，而且我已经成功地这样做了好几年了。在这一点上，这几乎是第二天性。

我很乐意听到其他使用这种技术的人的意见，我也很乐意听到那些开始将这种技术运用到实践中的人的意见。你遇到过什么挑战吗？想出其他人应该知道的事情了吗？让我知道！