# 编写干净的代码(第 2 部分)

> 原文：<https://dev.to/gonedark/writing-clean-code-part-2-9fn>

在[编写干净代码的第 1 部分](https://dev.to/gonedark/writing-clean-code)中，我概述了格式化、命名和避免嵌套代码的三个简单实践。这一切都是为了提高代码的可读性。

在第 2 部分中，我想更深入一点，介绍一下*分组*。当我说分组时，我实际上是在谈论*封装*的面向对象编程范例。我们将代码分组为函数还是类通常并不重要。重要的是我们是否提高了代码的可读性。

为了衡量我们的变化，我们应该问:

> 我们是否提高了可读性？

不可否认有点主观，但你会促使自己保持客观。过去两年我一直在结对编程。开发人员倾向于在基本可读性上达成一致。我们在边缘有分歧。这些细微差别会引发一些非常棒的讨论。

*什么归组*往往容易识别。我们都可以指出我们不喜欢的代码。我们说*如何分组*代码并不重要。剩下的问题是*什么时候分组编码*。我什么时候通过分组来清理代码？

让我们看看分组代码的三个*动机*。

## 改善沟通

任何需要额外上下文的代码都适合分组。我更喜欢我的代码不是以要求我了解业务逻辑的方式编写的。再简单的实现，我也永远无法理解。通过将这些代码分组，我们提供了一个额外的抽象层。一种保护我们自己和未来开发人员免受系统固有复杂性影响的方法。

考虑我们之前的代码示例:

```
function canView($scope, $owner_id)
{
    if ($scope === 'public') {
        return true;
    }

    if (Auth::user()->hasRole('admin')) {
        return true;
    }

    if ($scope === 'private' && Auth::user()->id === $owner_id) {
        return true;
    }

    return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然逻辑很简单，但我们通过将它提取到上下文命名的 helper 方法中来改进通信。

```
function canView($scope, $owner_id)
{
    if ($scope === 'public') {
        return true;
    }

    if (isAdmin()) {
        return true;
    }

    if ($scope === 'private' && isOwner($owner_id)) {
        return true;
    }

    return false;
} 
```

Enter fullscreen mode Exit fullscreen mode

像`isAdmin()`和`isOwner`这样的方法传递业务逻辑，使其易于理解。一旦理解，我可以很容易地将其应用到代码库的其他领域。最终，我们不仅仅改进了交流，我们还*向*开发者传授了代码。

需要指出的是，我没有将所有代码分组。每个群体都有心理成本。每家公司都需要提供足够的价值来弥补其成本。在这种情况下，我没有将逻辑分组到一个`hasScope()`函数中，因为它不仅没有改善通信，而且方法签名就像表达式一样冗长。

## 情侣数据

编程的另一个原则是*低耦合*。耦合还不错。事实上，当数据或代码真正属于一起的时候是很好的。我们可以通过发现逻辑连接或类似的*变化率*来识别耦合区域。

考虑下面的代码示例:

```
function plot($x, $y, $z)
{
    // ...
}

function transfer($amount, $currency)
{
    // ...
}

function substring($string, $start, $length)
{
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这里只有函数签名，因为我想把重点放在参数上。您可能不会马上看到分组机会。别担心，这是因为我们都受到原始痴迷的困扰。使用原语没有错。但是我们只*使用它们的倾向可能会阻止我们将这些数据分组到一个对象中。* 

```
function plot(Point $point)
{
    // ...
}

function transfer(Money $money)
{
    // ...
}

function substring($string, Range $range)
{
    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

通过将数据封装在一个对象中，我们不仅提高了耦合性，还为额外的逻辑提供了空间。我们可以将与该数据相关的任何内联逻辑移动到对象中。花点时间阅读马丁·福勒的 [Range 对象](https://martinfowler.com/eaaDev/Range.html)以获得更深入的示例。

## 组织机构代码

最后但同样重要的是简单的组织。不要害怕将类似的代码分割成自己的函数或类。只要它有自己的重量，它就有可能改进代码库。

发现这些比发现*数据耦合*更高级。在这里，我们采取了更多的视觉方法。如果某样东西看起来不符合当地的审美，那它可能属于别处。

考虑下面的代码示例:

```
namespace App\Models;

class User
{
    public function find($id) {}

    public function create() {}

    public function save() {}

    public function destroy() {}

    public function displayName() {}

    public function displaySignature() {}

    public function displaySalutation() {}

    public function createBadge() {}

    public function printBadge() {}
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个模型。通常，模型主要包含 CRUD 方法(创建、读取、更新、删除)。虽然模型包含额外的方法是完全可以接受的，但是我们可能会注意到模型中这些其他方法之间的关系。

仅通过名称我们就可以发现与*显示*和其他*标记*方法相关的方法。我们也许可以在其他地方组织这些代码。在这种情况下， *display* 方法可以被提取到一个 *Presenter* 类。*徽章*方法指向一个*打印机*类或其自身的`Badge`对象。

试试这些动机。也许有些适合你的代码库，也许有些不适合。对于*我们是否提高了代码可读性*的答案可能因开发者和项目而异。但是总是问这个问题…

***想看更干净的代码？**在推特上关注[我，了解每周清理技巧，或者让我们](https://twitter.com/gonedark)[结对](https://coaching.pureconcepts.net)一起写一些干净的代码。*