# 原子 CSS 的问题是

> 原文：<https://dev.to/adambsilver/the-problem-with-atomiccss>

自从发布了 MaintainableCSS 之后，我收到了一些来自原子 CSS 拥护者的批评。一个人说他们看完之后在屏幕上吐了一地，这还算有趣。

当我写语义类名时，将它们与非语义类名进行比较是有意义的。一旦我们分析了我们*不应该*做什么，我们通常会推断出我们*应该*做什么。

作为回应，原子 CSS 提倡批评语义 CSS。我其实不介意批评。被挑战的感觉真好。这当然挑战了我自己的想法，但迄今为止，我的想法是不变的。

在这篇文章中，我将全面解释这是为什么。我还将讨论原子 CSS 倡导者的一些评论。这是:

### 1。“语义是一个误导的词”

在[理解语义](http://adamsilver.us9.list-manage.com/track/click?u=b8fb04f39bf86109693e00ba5&id=711b355d7c&e=518c1e4999)中，onie Watson 指出*语义*是指代码的 ***，意在反映结构和意义*** *。*

这就是为什么单词*包装器*是语义的。它是一个元素包着另一个元素，*总是*。无论 CSS 清除大屏幕上的浮动“列”还是将它们堆叠在小屏幕上，它总是一个包装器。

因此，一旦我们编写了 HTML，它不需要改变。其中作为一类红色是没有语义的——至少在 HTML 的上下文中是如此。

在 HTML 的上下文中，语义类名描述的是它*是什么*，而不是它看起来是什么样子(或者它的行为方式)。这是对元素本身的补充。元素描述的是它是什么，而不是它看起来像什么。

### 2。“语义类名导致网站运行缓慢”

我建了很多网站。它们从来都不慢，因为我们使用了语义类名。

### 3。命名不当的语义类并不能证明这种方法不好

最近我读到`.group`并不比`.clearfix`更有语义——我同意，因为两者都不是好的类名。命名不当的类名对方法本身的有效性没有影响。

### 4。语义类是单一用途的

原子类是作为单一目的来销售的。语义类也是单一用途的。它们通常不包含一个规则，但是它们*是*单一目的的。还有…

### 5。原子 CSS 并不意味着一个规则

原子 CSS 框架可能包含每个类的几个规则。使名字容易引起误解。

### 6。“原子 CSS 确保设计的一致性”

理论是，如果我只被允许使用预定义的类，视觉设计更有可能保持一致。

这有一定的意义，因为它限制了开发人员可以使用的样式。至少理论上是这样。

然而，这一切都在应用程序中。没有什么可以阻止我添加，例如，一个新的`margin-bottom`类或者使用*错误的*类。

或许有机会创造一种工具而不是一种惯例来解决这个问题？

### 7。孤立地分析 CSS 性能

原子 CSS 最受吹捧的方面是大小和性能——特别是首次绘画的时间。倡导者经常孤立地讨论这个问题，这往好里说是误导，往坏里说是欺骗。

首先，HTML 的大小显著增加。记住，CSS 是可缓存的，通常服务于整个网站。HTML 通常是独特的、动态的和个性化的。不能缓存。

其次，即使在大型网站上，节省的 CSS 数量也相对较少。我肯定有网站使用了 10 个 CSS 的故事，但是在建立了许多网站之后，我发现 CSS 很少是罪魁祸首。

此外，CSS 的大小不是唯一的性能指标。还有许多其他的问题，需要全面考虑。

### 8。原子类很难读懂

原子类名通常是缩写的。缩写很难读。他们必须被理解*和*在精神上映射。我们应该力求[清晰胜于简洁](http://adamsilver.us9.list-manage.com/track/click?u=b8fb04f39bf86109693e00ba5&id=9be803745a&e=518c1e4999)。

例如，`blk`是什么意思？是指*黑*还是*挡*？假设它的意思是*黑色。那是黑色文本还是黑色背景。*

我们可以使用更容易阅读的冗长名称，但这加剧了 HTML 膨胀和性能的问题——这是缩写的首要原因。

或者可能是为了节省击键，但是任何像样的编辑器都会有自动完成功能。

### 9。原子 CSS 在 HTML 中重新创建 CSS

原子 CSS 重新创建了 CSS 中的相同结构，以便在 HTML 中使用类。CSS 是为样式设计的。为 HTML 重新创建一个惯例是很乏味的，这会鼓励开发人员使用错误的工具。

### 10。无论如何，我们需要语义挂钩

为了编写功能测试和增强网站，我们需要语义类。因此，将会有一个混合的类，每个类都为不同的事情保留。

不一致的代码很难推理，如果有两种方法做某事，不可避免地会被误用。

### 11。语义 CSS 很小

我最近建了一个总容量为 48kb 的网站。它有许多组件，繁琐的风格和断点。这要怪视觉设计师。

尽管如此，CSS 并不慢。我并没有太关注 CSS 的性能。毫无疑问，我们可以省钱。但是不麻烦，它一点也不伤害用户*。*

 *### 12。语义 CSS 不违背干

试图重用一个 CSS 规则，就像试图在不同的 Javascript 对象中重用一个变量。这根本不违反。

CSS 为我们抽象了所有的规则和排列，所以我们可以指定我们想要什么，什么时候想要。谢谢 CSS。

### 13。语义 CSS 很容易删除

语义定义的组件很容易删除，因为相关的 CSS 属于正在讨论的模块。原子 CSS 与大量元素交织在一起，使得代码难以删除。

在删除相关的 CSS 之前，你首先需要查看每个类，模块中的每个元素，以确定它是否在其他地方被使用。只有这样你才能决定是否删除它。

[好的代码很容易删除](http://adamsilver.us9.list-manage1.com/track/click?u=b8fb04f39bf86109693e00ba5&id=7a45c5a206&e=518c1e4999)，因为它是*而不是*交织在一起的。

### 14。原子 CSS 是一种响应式设计反模式

正如 Ben Frain 在[中所说，Atomic CSS 是一种响应式设计反模式](https://benfrain.com/oocss-atomic-css-responsive-web-design-anti-pattern/)，*在某些断点处进行非常具体的更改，并将它们绑定到一个必须添加到 HTML 中的类上，这似乎是不必要的复杂。*

他继续说*你不可避免地会在你的样式表中留下大量过时的类。*

### 15。更难设计伪类的样式

对于您想要更改的每一种样式，您都需要一个等价的、冗长的、难以阅读的类名。比如`.red-text-when-hover`和。`black-bg-when-focus`等。

如果样式需要在不同的断点改变，那就更难了。比如`.red-text-when-hover-on-large-screens`。

### 16。基于状态的样式更难

考虑一个状态为空的篮子。每个因状态而不同的样式都需要自己的类。

### 17。更难根据阅读方向来设计风格

大卫·马克的[推特](https://twitter.com/Cinsoft/status/835968849086394368?s=09)报道了这一点:

> …在 RTL 配置中，“向左拉”总是包含浮动:右。这是为什么它没有意义的线索。

### 18。`enhance`更难了

如果我们想使用`@supports`，我们需要一个`.supports-x-do-y-class-name`。

### 19。改变布局机制更难

正如 Ben Frain 在同一篇文章中所说的，*假设[…]我们将我们的产品[…]从基于浮动的布局改为基于 Flexbox 的布局。我们现在有两倍的维护负担。*

### 20。原子类是误导和多余的

例如，`overflow-hidden`用于清除浮动的子节点。然而，在小屏幕中，孩子是堆叠的，而不是浮动的。这对开发者来说是误导，对用户来说是多余的。

### 21。每个元素都需要类

使用原子 CSS，每个元素都需要几个类。但是有时候我们不需要添加一个钩子，因为我们可以这样做:`.blah div`或者这样:`input[type=submit]`。

例如，Markdown 也迫使我们通过一个共同的祖先(带有语义类名)来设计元素的样式。

### 22。这让检查员很吵

很难确定一个模块在哪里开始和结束，因为在 HTML 中没有任何东西表明这一点。内容变得模糊不清，检查员需要查看更多行代码。

### 23。很难找到 HTML

模板和良好的文件结构很有帮助，但是我们经常使用检查器来查找 CSS。原子让我们更加困难。我们不应该仅仅依靠文件结构来寻找 HTML。

### 24。在 HTML 和 CSS 之间切换并不难

有些人推荐原子 CSS，因为 CSS 和 HTML 之间切换较少。

在开发人员必须做的所有事情中，按下 *cmd+tab* 并不费力。

此外，这几乎是无稽之谈。不需要切换到 CSS 的唯一方法是如果您知道所有可用的类名。我怀疑大多数人都是这样。

### 25。“原子 CSS 使跨项目重用 CSS 变得容易”

倡导者认为原子 CSS 使得跨项目重用 CSS 变得容易。这是一个很好的想法，但它实际上是无用的，除非每个网站看起来都一样。大部分网站都没有。

### 26。主题化 CSS 是困难的

当我为几个电子商务网站开发白标解决方案时，重用的是 HTML，而不是 CSS。这是因为 HTML 是相似的，而不是 CSS。

### 27。很难创造新的组件

在新工作的第一天，你必须开发一个新的组件。

不是给组件一个语义类并对其进行样式化，而是必须添加大量的类，并且首先检查这些类是否存在。不能简单的知道有什么类名。因此，这项工作更加困难。

### 28。很难编辑现有的组件

这个时候你需要*换*一个组件。您设法找到了 HTML，但是这次您必须找出哪些类需要删除和添加。

要添加一个类，您必须先检查某个类是否存在，然后才能使用或不使用它。如果它在那里，你可以使用它。如果不是，就创建一个。

我不知道有谁会阅读所有可用的 CSS，看看是否有他们可以重用的东西。

### 29。“语义类比原子类名长”

这是一个典型的原子 CSS 片段，来自一个具有*基本*样式的网站:

```
class="w5 w6-m w-50-l center overflow-visible mt3 mt4-m dtc-l v-mid-l tr-l" 
```

我从来没见过语义类名接近这个的。有吗？

### 30。解决互联网探索问题更加困难

有时我们会添加条件 CSS 来修复 Internet Explorer 的错误。我们不能以原子类名为目标来做到这一点。

### 总结

在许多情况下，原子 CSS 会减小 CSS 文件的大小。那是可以预料的。就像如果决定内联我所有的样式，我希望 CSS 文件的大小为零字节。

问题是，它引入了许多其他问题，我们忽视这些问题是愚蠢的。

一旦性能成为问题，它就只是一个问题。可能有许多因素在起作用。

我不是说等待问题的出现，但我也不是说我们应该抛弃像语义 CSS 这样已经尝试过、测试过的技术。

关于 CSS，还有其他方法可以让网站加载更快。例如，我们不必一次加载整个网站的 CSS。我们可以包含一个页面需要的 CSS 并逐步缓存。

专注于 CSS 不一定是我们精力最大化的地方。也许这一页的内容太多了。也许视觉设计很浪费，没有让用户受益。你可能正在使用一个框架，CSS 或者其他你不需要的东西。

总而言之，不得不在所有的权衡中导航，以去除一些 CSS，这极大地增加了 HTML 的大小，只是用一个问题交换了几个其他的问题。

在大多数情况下，无论如何我们都需要语义类来完成这项工作。我们也可以在 CSS 中使用它们。

*本帖最初发表于[medium.com](https://medium.com/simple-human/the-problem-with-atomic-css-d0c09c7aa38e)T3】**