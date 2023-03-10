# 软件设计模式

> [https://dev . to/missing SNC/软件设计模式 dj6](https://dev.to/mangelsnc/patrones-de-diseno-de-software-dj6)

[![](img/d58906aa32d29a818cbae650e9c76fde.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--MyA1hKuv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ao0SR0QrsG8mr8Evrhpvi5Q.png)

#### 导言

作为程序员，我们肯定会意识到，我们常常以不同的方式解决同一个问题。在我们的职业生涯开始时，这可能是一个积极的事态发展:我们尝试了不同的方法来解决一个问题，我们可以比较我们以前解决方案的利弊，但随着时间的推移，我们喜欢直接解决问题，采用最可扩展、最可测试和最可重复使用的解决方案。当然，不必向每位同事解释我们是如何破案的也是可取的，但这并不是模式所能解决的(除非我们的同事也认识他们)。

#### 是软件设计模式吗？

因为这些都不是解决软件开发中常见设计问题的方法。

使用图案的好处显而易见:

*   它们构成了一系列广泛的问题和解决方案
*   标准化特定问题的解决
*   谴责并简化学习良好做法
*   他们提供了开发人员之间通用的词汇
*   避免□

#### 关于设计模式的书籍

首批论述图案主题的书籍之一是著名的《T2》第四季第 3 集《T1》设计图案:Erich Gamma、Richard Helm、Ralph Johnson 和 John Vlissides。随着时间的推移，所处理的一些模式现已被认为是抗阿托品*。*

一本书以更温和的方式来了解图案，是我个人非常喜欢的《T0 头第一设计图案》。

如果我们想学习重构我们的代码，使这种利用设计模式的方法是 Joshua Kerievsky 的“T1 模式重构”，作者在其中为我们提供了关于每个模式的信息:

*   理论解释(包括 UML)
*   一种*处方*应用模式
*   可重构代码示例
*   处方应用于上述代码。

这些是我关于图案的三本书，但关于它有很多文献。

### 模式类型

根据图案的目的，这些图案分为三类:

*   创意模式
*   结构样式
*   行为模式。

让我们详细了解一下这些类型中的每一个。

#### 创作模式

顾名思义，这些图案用于解决或简化创建或实例化对象的任务。

这些模式强调封装实例化逻辑，隐藏每个对象的特定详细信息，并允许我们使用抽象。

最著名的创作模式包括:

*   [**工厂**](https://medium.com/all-you-need-is-clean-code/patr%C3%B3n-factory-5b39eef765df) :将创建逻辑与业务逻辑脱钩，避免客户知道其依赖对象的实例化细节。
*   **抽象工厂**:它为我们提供了一个界面，可以委托创建一系列相关对象，而无需具体说明具体实现。
*   **工厂方法:**阐述了一种创建方法，将该方法的实现委托给子类。
*   **Builder** :将复杂对象的创建与其结构分离开来，以便同一构造过程可以帮助我们创建不同的表示。

#### 结构模式

结构样式有助于定义对象的组合方式。

最常见的结构样式包括:

*   **适配器**:帮助我们定义一个中间类，使两个具有不同接口的类能够通信。此类充当中介，使 a 类可以在不知道其实现细节的情况下执行 b 类方法。也称为**包装。**
*   **Decorator** :允许您在不改变其它实例行为的情况下向对象(“T2 装饰对象”)添加额外功能。
*   **面**:面*面*是创建简化界面以处理更复杂代码的另一部分的对象。

#### 行为模式

行为模式可以帮助我们定义对象之间的交互方式。

一些最著名的例子(仅举几个例子)是:

*   **Command** :它们是封装动作的对象以及执行动作所需的参数。
*   **观察者**:对象能够订阅另一个对象将要发布的一系列事件，发生这种情况时会发出警告。
*   **策略**:允许选择在运行时执行某些动作的算法。
*   **模板方法**:指定一种算法的框架，允许子类定义如何实现实际行为。

### 安帕坦

并非所有的模式都会好，有所谓的**，一种导致我们实施不好解决方案的坏双胞胎。**

 **[![](img/163db1d537950550a90c982ab7b15b0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KUsnUQ3H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/400/1%2A_Wrg3MbG-PuiLJk_or_Cwg.jpeg) 

<figcaption>总有一个坏双胞胎。</figcaption>

了解抗阿托品也是一件好事，以便及时发现未来的头痛。

我们以前讨论过 GoF(四人帮)的书，以及一些模式如何被认为是反阿托品。一个很明显的例子是模式*单曲*。

Singleton 模式是一种创造性的模式，它帮助我们使一个类只有一个实例。这在处理数据库连接或其他服务的对象等情况下很有用，因为它通过限制创建更多对象来减少内存使用量。

另一方面，该模式使用私有和静态方法，将对象缩小到几乎一个全局变量，使测试变得困难……。一次性使用本身并不一定是坏事，但在短期内滥用可能是致命的，因此许多程序员都认为它是一种厌恶。

### 下一篇文章

现在我们介绍了设计模式的概念及其不同类型，接下来将是一系列专为某个模式设计的员额:理论和实现。

接受请求；）

* * ***