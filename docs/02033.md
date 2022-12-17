# Swift 中协议的更好重用标识符

> 原文：<https://dev.to/clue/nicer-reuse-identifiers-with-protocols-in-swift-15nk>

[![](img/9e97b01b96546232bc85b77b01e57c1c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--VE8MOxz_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Axygb5sEsnYtGDvmGcdv9fA%402x.png)

最近，我经历了建立一个新的 UICollectionView 的动作。我已经为我的单元格编写了一个视图模型，并且已经准备好了 UICollectionViewCell 子类。剩下要做的就是实现 cellForItem(位于:)。

顺便说一句:如果你以前没有使用过 UICollectionView，但更像是一个 UITableView 类型的人，那么你可以用 Table 替换 Collection，用 Row 替换 Item，这篇文章应该仍然有效。

作为一名负责任的 iOS 工程师，我知道我需要做的第一件事是让我的 collectionView 将一个单元格出队。为此，我必须调用 dequereusablecell(withReuseIdentifier:for:)，传递一个字符串“重用标识符”。为了让 collectionView 知道我在说什么，我还必须在集合上调用 register(_:forCellWithReuseIdentifier:)，这样它就知道将这个重用标识符映射到我的 UICollectionViewCell 子类。

该子类大致如下:

```
final class CustomCollectionViewCell: UICollectionViewCell {

// code etc.

} 
```

Enter fullscreen mode Exit fullscreen mode

另一个题外话:CustomCollectionViewCell 只是一个示例名称。请不要这样命名类。

我决定这个单元格的一个合理的重用标识符是“CustomCollectionViewCell”。所以我使用了它，并调用了这两个方法。这些电话看起来是这样的:

```
// in a set up method
collectionView.register(
 CustomCollectionViewCell.self, 
 forCellWithReuseIdentifier: "CustomCollectionViewCell"
)

// in cellForItem(at:)
collectionView.dequeueReusableCell(
 withReuseIdentifier: "CustomCollectionViewCell"
 for: indexPath
) 
```

Enter fullscreen mode Exit fullscreen mode

这显然很糟糕。我们有一根神奇的绳子挂在那里，它在不止一个地方。显然，这需要一些重构，所以这正是我所做的。第一步只是将该字符串移动到 CustomCollectionViewCell 类的静态常量中。

```
final class CustomCollectionViewCell: UICollectionViewCell {

static let reuseIdentifier = "CustomCollectionViewCell"

} 
```

Enter fullscreen mode Exit fullscreen mode

好吧，好的开始。这意味着我们可以使用 customcollectionviewcell . reuse identifier 在代码中的任何地方访问标识符。这已经是对我们之前的一个巨大改进。但是它确实提出了另一个问题。如果类名后来改变了怎么办？

我们可以只记得改变它，但让我们现实一点:我们是人，我们并不总是记得这样的事情。即使它就在我们面前。这就是为什么我们让计算机为我们做这些事情。这就引出了下一个问题:我们能让编译器帮助我们吗？

好吧，如果答案是否定的，这将是一个非常短的博客。

那么我们该怎么做呢？见上，我们将对 CustomCollectionViewCell 类型的引用传递到 register(_:forCellWithReuseIdentifier:)？如果我们能以某种方式将这种类型转换成字符串并使用它，那不是很好吗？那太好了。

(戏剧性的停顿。)

是的，我们当然能做到。我们可以用 String(描述:CustomCollectionViewCell.self)替换 reuseIdentifier 定义中的裸露字符串，得到完全相同的输出。现在，如果类名改变了，编译器不会识别类型，会让我们知道。成功！

好的，现在你可能正在看我们到目前为止所做的，并且想:“马修，这很棒，但是标题说的是‘协议’,到目前为止我们只是在进行一点重构。”你是对的，毫无疑问。把以上所有简单地看作是接下来要做的事情的动力。

因此，在使用 String(描述:TypeName.self)生成重用标识符一段时间后，我开始想知道是否有一种方法可以消除在每个 cell 子类中定义新的重用标识符的样板文件。

我从编写下面的协议开始:

```
protocol ReuseIdentifying {
 static var reuseIdentifier: String { get }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后像这样扩展:

```
extension ReuseIdentifying {
 static var reuseIdentifier: String {
 return String(describing: /\* er... what goes here? \*/)
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

我在这里被困了一段时间。我需要一种方法来动态引用实现协议的类型。显然，使用 ReuseIdentifying.self 是行不通的，因为它每次都会返回“ReuseIdentifying”。

过去，我在协议中使用 Self 来表示“实现类型”所以我想知道我是否能逃脱类似 Self.self 的东西。这看起来完全荒谬，但是…

剧透:完全成功了。

这是最终的协议扩展:

```
extension ReuseIdentifying {
 static var reuseIdentifier: String {
 return String(describing: Self.self)
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，通过将该协议应用于我的 CustomCollectionViewCell，我可以删除重用标识符定义，一切都按预期工作。如我所愿，返回的 reuseIdentifier 是“CustomCollectionViewCell”。

还有更好的消息:我们可以更进一步。假设我们希望每个 UICollectionViewCell 子类都有一个重用标识符。我们实际上不需要单独应用这个协议。我们可以这样做:

```
extension UICollectionViewCell: ReuseIdentifying {} 
```

Enter fullscreen mode Exit fullscreen mode

神奇！✨

### 开放式问题

我喜欢测试东西，所以我想针对这个协议扩展编写一些单元测试。

我尝试在测试体中定义一个类。大概是:

```
func test\_reuseIdentifier\_classIsCalledSomeClass\_isSomeClass() {
 class SomeClass: ReuseIdentifying {}
 XCTAssertEqual("SomeClass", SomeClass.reuseIdentifier)
} 
```

Enter fullscreen mode Exit fullscreen mode

据我所知，这没什么不可行的。但是 String(description:)在这一点上开始表现得有些混乱。reuseIdentifer 的返回值是“SomeClass #1”。

我认为这可能是在方法中定义类的问题，所以我把它移到了文件范围，并再次运行测试。而且成功了！

所以，如果有人知道为什么在方法中定义类会导致在描述的末尾附加“#1 ”,我会非常有兴趣去发现。

* * *