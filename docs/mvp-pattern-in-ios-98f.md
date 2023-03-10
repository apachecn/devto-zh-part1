# iOS 中的 MVP 模式

> 原文：<https://dev.to/apium_hub/mvp-pattern-in-ios-98f>

[手机应用](https://apiumhub.com/mobile-app-development-barcelona/)的发展近年来发生了很大的变化和演变。由于[开发者](https://apiumhub.com/software-developer-jobs-barcelona/)的兴趣和移动平台的增长，开发团队越来越重视创建可重用的组件，并在软件开发中使用最佳实践，就像那些已经存在于桌面应用、web 应用和后端应用中的一样。这种兴趣带来了激增、更多样化的模式，能够获得更易测试、模块化和可重用的代码，这导致了应用程序性能的提高、错误数量的减少和高质量的软件开发。这篇文章是关于 iOS 开发的 MVP 模式。

在 [Apiumhub](/) 这里，我们一直专注于创造质量和[工作软件](https://dev.to/apium_hub/working-software-go-live-strategy)，以密集的方式使用[最适合每个平台](/software-projects-barcelona/)的模式和软件开发技术，适应客户的需求。

让我们把重点放在解释我们已经在其他平台上使用的 MVP 模式上，这表明它相对较好地适应了移动技术。就像安卓一样，已经成为“默认标配”。

我们将向您解释我们如何将 MVP 模式应用于 iOS 应用程序开发，我们的主张以及我们认为它是其他模式的有效替代方案的原因，以及我们如何使用这种 iOS 模式插入我们自己的不同开发部分。

MVP 模式是在 90 年代作为苹果、IBM 和惠普的一个通用 C++计划发明的，作为 MVC 模式的替代方案。它是模型、视图、展示者的缩写，是这个模式的重要组成部分。这种模式有不同的实现方式和一些重要的差异，如[监督控制器](https://martinfowler.com/eaaDev/SupervisingPresenter.html)或[被动视图](https://martinfowler.com/eaaDev/PassiveScreen.html)。

MVP 模式和其他 UI 模式的主要区别在于视图和模型之间的完全独立性，换句话说，视图不知道模型的存在，反之亦然。

在我们的例子中，在 [Apiumhub](/) ，我们采用了这种模式的变体，在一个名为 [Claymore](http://claymore.codeplex.com/wikipage?title=DOC_UNDERSTAND_MVP_PATTERN&referringTitle=Documentation) 的框架中实现。(这里有一个 [C#](http://claymore.codeplex.com/wikipage?title=DOC_SAMPLE_CLOCK&referringTitle=Home) 中的例子)

## 纯 MVP 模式

就像在 MVP 模式的所有定义中一样，在我们的例子中，它有 3 个独立的和明显不同的部分，但是有一些特点:

**视图**

视图是一种布局，其任务是呈现 UI 并对用户事件做出反应。在 iOS 的情况下，视图将由一个协议和一个 ViewController 组成，前者公开用户事件的方法，后者负责具体化和呈现 UI 组件，并捕获传递给模式的下一个组件(presenter)的事件。

**主讲人**

它负责在视图发出的事件之间建立一个连接器，并将它们与模型连接起来，特别是与模型的服务连接起来。在这里的 [Apiumhub](/) 中，我们将 presenter 理解为一个连接视图和模型的软件，但是它缺乏逻辑，视图数据的整个状态及其行为在服务中处理，并通过 presenter 返回转换后的数据或适当地响应事件。在 iOS 中，通常，presenter 是在 init 方法中接收视图和服务并连接所需方法的类。presenter 通常在主 ViewController 的扩展类中使用，它发生在连接设置完成时。

**型号**

它是包含业务逻辑的系统，由不同的软件组成，具体取决于应用程序的复杂程度。在一个“面向领域的系统中，我们将不得不构建战术 [DDD](https://dev.to/apium_hub/an-introduction-to-domain-driven-design--its-benefits) 的层次，每个前端应用的特殊性会带来一些变化。在移动应用中，我们在 Apiumhub 中看到的模型的主要部分是:

**1。服务:**它们负责在应用程序的域对象中执行业务逻辑(如果有的话),以及是否需要调用存储库(o gateway)来访问外部服务或本地数据库。在 iOS 的情况下，服务将由一个类和一个协议组成。该类将包含协议公开的方法的实现。

**2。实体:**它们是领域对象。它们包含反映应用程序状态的逻辑。通常，这些对象是一种 dto，因为事务的逻辑在服务器中，然而，也有一些例外。这些对象不应该出现在视图中，但是，当我们讨论没有逻辑的简单数据传输对象时，有时会出现例外。通常，实体被表示为一个类。

**3。存储库:**它们调用外部服务(API 或其他服务)或本地数据库(如果应用程序有这些服务的话)。就像服务将由一个类和一个协议组成一样。

**4。DTO o PresentationModel:** 这些是从服务返回的对象，表示在应用程序执行或任何状态改变后视图的状态。通常，它们由一个或多个实体构建而成。在这种情况下，我们将使用结构来实现这种类型的对象的表示，因为当表示视图的状态时，它们之间不应该有层次结构。

这种“纯 MVP 模式”的主要特征是，演示者没有逻辑，就像一个普通的插头连接器。这使得演示者可以作为一个独立的组件，它可以在应用程序的多个部分中重复使用。

### **主持人的一些优点有:**

它是一个直接表达其意图和责任的组件，而没有添加可能使我们认为同时执行多项任务的逻辑。

演示者集中了事件的流程，这有助于我们:

*   调试，相反它会变得更复杂，尤其是当你使用绑定系统的时候。
*   跟踪事件的来源更容易，事件和负责管理它的服务之间的关系一目了然。
*   避免“隐藏的事件链”,因为它们缺乏逻辑并处理服务中的数据状态
*   相同组件可以在不同的上下文和服务中重用。
*   严格来说，您不需要测试，尤其是在静态类型的环境中，因为本质上是声明性的，这使得很难通过逻辑或数据状态的改变引入错误

### 这种模式带来的一些全局特性是:

*   当不在服务器中管理时，数据的状态总是从模型级别管理，更具体地说是在服务层。
*   避免了一连串的事件，通常来自用户的迭代被认为是一个事件
*   因为我们避免了事件链，所以对服务器的调用次数被最小化，因为数据的逻辑和状态是在服务级解决的。
*   减少耦合并增加内聚力，因为每个软件都有清晰的职责和能力来在模型和视图之间分配逻辑。
*   改进了[单元测试](https://apiumhub.com/tech-blog-barcelona/top-benefits-of-unit-testing/)的使用，以验证每个软件的正确功能。

总之，在 Apiumhub 中采用这种 MVP 模式，使我们能够将它扩展到更多种类的平台，创建一个更加模块化的系统，明确组件的责任，这允许测试的覆盖范围更大和可重用。

感谢所有这些特性，在 Apiumhub，我们相信它适应并满足我们的需求，这并不意味着我们保持模式的持续发展，并通过在每个平台中引入新的特性来改进它。这使得我们可以将这种模式与已经在 iOS 架构中使用的其他模式进行比较，例如 [Viper](https://apiumhub.com/tech-blog-barcelona/viper-architecture/) 、MVVM、Clean Architecture 等等，我们将在下一篇文章中尝试评估这些模式。

如果你对移动开发感兴趣，我强烈推荐你点击[这里](http://eepurl.com/cC96MY)订阅我们的每月简讯。

## 如果你觉得这篇文章很有趣，你可能会喜欢…

[iOS Objective-C app:成功案例研究](https://dev.to/apium_hub/protected-ios-objective-c-app-cornerjob-successfull-case-study-89e)

[年度移动应用开发趋势](https://dev.to/apium_hub/mobile-app-development-trends-of-the-year)

[Banco Falabella 可穿戴设备案例研究](https://apiumhub.com/tech-blog-barcelona/banco-falabella-wearable-ios-android/)

[移动开发项目](/software-projects-barcelona/)

[面向 iOS 应用的 Viper 架构优势](https://apiumhub.com/tech-blog-barcelona/viper-architecture/)

iOS 中的 post [MVP 模式最早出现在](https://apiumhub.com/tech-blog-barcelona/mvp-pattern-ios/) [Apiumhub](https://apiumhub.com) 上。