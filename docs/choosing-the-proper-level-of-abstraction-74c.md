# 选择适当的抽象层次

> 原文：<https://dev.to/lpasqualis/choosing-the-proper-level-of-abstraction-74c>

这篇文章最初发表在 [CoderHood](https://coderhood.com) 上，名为[选择适当的抽象层次](https://dev.toPOST-URL)。CoderHood 是一个致力于软件工程人类层面的博客。

[![](img/c69d728e5c4ca2f3044422c1a78ef429.png)T2】](https://www.coderhood.com/choosing-the-proper-level-of-abstraction/)

## 现实的模型&抽象的层次

物质世界和规范它的规则是复杂的，非常难以把握。为了理解它，我们人类试图用心智模型来解释它。模型是用我们创造和理解的术语定义的逻辑结构。我们使用这些模型作为现实的代理，试图把握和预测其复杂和多面的规则。

模型不仅仅是一个工程概念；它们是日常生活的基础，是我们所经历的世界的自然简化。我们每天都在为一切事物建立模型。例如，我们对某个我们见过的人的记忆是我们在脑海中构建的那个人的模型。这是一个简单到足以储存在我们记忆中的描述，也是复杂到足以细致入微的描述。

## 一块岩石的模型

鉴于我们大脑的局限性，我们为现实的许多方面创建逻辑模型。例如，我们有基于对象最显著特征的对象模型。一个非常有效的摇滚模型就是“摇滚”这个词本身。

> 岩石

一个更细致的模型可能是一个简单的形状，这给了我们更多关于岩石外观的信息。

[![](img/31fbc08a227448035e1b5b494557d58f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--NKvU2ojT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rfyxlxqvpd2xe6rfyyh3.png)

但这仍然很粗糙。很难说那是一块石头，还是一个鸡蛋，还是别的什么东西。任何具有这种形状的东西都可以被描述为椭圆形，这使得模型无法捕捉岩石的独特特征。一个更细致的模型可以被想象成它的图画。

[![](img/b1ef9ff6de03eacf79df0fed89be9e9c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--rKTiIFsz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b6yql0nm2qwosddejqk4.png)

这幅画并不比圆圈更详细，但它包含的信息提醒我们，岩石并不具有光滑的轮廓，并显示出表面的一些缺陷。尽管如此，由于我糟糕的艺术技巧，这幅画可以代表许多不同的东西。

圆形岩石的图片是一个更好、更详细的模型，它包括颜色、纹理和周围的背景。此外，一张照片的优势是可以被机器捕捉，而不需要经过人类解读的过滤。

[![](img/11e21c1ec12d24e6154ada56974a3f13.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TiZgp9S7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9i07tmg5wm142r35d14s.jpg)

然而，这个模型不能完全描述岩石。例如，它是二维的，只显示特定的一面。它不包含任何关于重量、三维特征、内部组成、空间位置等的信息。

要完整描述某一特定时刻的岩石，需要绘制出它的每一个原子及其在空间中的相对位置。考虑到石头会随着时间的推移而变化，完整的描述需要这样的地图来记录石头存在的每一个瞬间。这就需要确定这块岩石是什么时候开始存在的，而且只有当它不再被认为是一块岩石时才能确定。

很明显，这是一个荒谬的模型。这样的描述将是无限复杂的，用我们有限的精神处理能力是无法使用的。换句话说，对于大多数目的来说，它将毫无用处。

## 模型和抽象概念

术语“模型”和“抽象”经常被重载；在本文件中澄清它们的定义是很重要的。**警告**:以下定义比我希望的更正式；然而，我发现有必要精确地定义它们以避免混淆。

*   **领域**:概念、对象、活动或知识的特定范围。
    *   例如，“建筑物”是由所有可能的建筑物组成的域。
*   **模型**:一个领域子集的一组独特的特征，包括它们的约束。在明确定义的模型中，约束是必要的，以将变化的数量限制到有限集。
    *   例如，房屋模型可以包含一组房屋的独特特征:如颜色(16，777，216 个 8 位 RGB 值之一)、大小(50 平方英尺到 10，000 平方英尺的整数)、房间数量(1 到 10)。
    *   房屋模型所覆盖的建筑领域的子集是可以由它描述的所有房屋的集合。在示例中，颜色、尺寸和房间数量的所有可能组合。总共有 1，669，332，992，000 个可能的房屋。
*   **模型的实例**:一个可以被模型描述的特殊变化。
    *   例如，房屋模型的实例可以标识为红色(RGB #FF1010)，4000 平方英尺，有 6 个房间。
*   **模型的展开**是模型所有可能实例的集合。
    *   在房屋模型示例中，所有 1，669，332，992，000 个可能的房屋。
*   **模型 A 被称为模型 B** 的抽象，如果 A 的展开是 B 的展开的超集， **B 是由 A** 抽象的模型
    *   例如，房子是一个工作室公寓的抽象。单间公寓是房屋抽象的一个模型。

### 再举一例

*碳基生命体*是很多模型的抽象，比如:*植物*、*动物*、*微生物*等。*动物*是一个模型，可以描述一个动物的一系列与众不同的特征。*动物*的域之一是*碳基生命体*域。*动物*也是狗的抽象，而*狗*是*动物*抽象中的模型。*狗*也是一个*德国牧羊犬*模型的抽象。一只名叫科迪的德国牧羊犬是德国牧羊犬模型的一个实例。

如果你做到了这一步，剩下的就舒服多了。我保证。

### 选择一个抽象层次

建模时应该选择什么样的抽象层次取决于您对所操作的环境的了解，以及将来事情会如何变化。

例如，如果你想创建一个软件来管理“最佳表演”狗比赛的参与者，建模一只狗就足够了。以狗为中心的竞争扩大到包括其他动物的可能性非常低，以至于抽象到所有动物不是你需要计划的事情。

另一方面，如果你正在编写管理宠物收容所客人的软件，那么 *Dog* 可能不是正确的抽象，即使当前所有的客人都是狗。你需要向前看一步。不久，这个庇护所可能会收留猫、鸟、蜥蜴，谁知道还有什么。在这种情况下，*动物*可能是一个更好的抽象。

## 在软件开发中选择一个抽象

在软件开发中，选择正确的抽象可能很棘手。如果你把它做得太简单，它不会让你创建一个模型来满足哪怕是最直接的需求。如果您将它限制在紧急的需求上，那么您可能不得不立即改变它，以实现模型的下一次迭代。然而，如果你使你的抽象过于一般化和包罗万象，建模解决方案可能会变得如此复杂，以至于你在完成之前就会破产。

根据我的经验，工程师选择的抽象级别是由以下因素决定的——好的或坏的:

*   他们的技能和经验。
*   他们的个性。
*   关于域的可用数据。
*   时间和资源。
*   偶然或故意的假设。

在这篇文章的其余部分，我将使用一个具体的例子和一个类比来形象化选择一个抽象意味着什么。

# 建模计算器

想象一下，一个客户要求你构建一个计算器应用程序(比如一个袖珍计算器)；能满足小学生需求的东西。

## 理想模式

客户心中有一个高层次的理想。他们不知道它，也不能清楚地描述所有的细节，但是如果他们花时间来记录理想的模型，它会是这样的:

[![](img/2e154cb306bc1d2e5eb69a506b008978.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Aj2md3X8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/maeut87fty3qu9wz9prk.png)

## 你拥有的信息

然而，客户邀请你共进午餐，并给你一个口头描述，而不是一份设计文档，你试图尽可能地记下来；因此，你的“规格”是一张沾有咖啡的餐巾纸背面的草图。

[![](img/ef32e621ec12effbd1be9ea0d39041d3.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--jDsE4TqD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4ut0nrgevadx2yat8nh6.png)

他们告诉你，他们想很快得到一些东西，它不必是完美的。你接受挑战。

## 你的挑战

现在你需要弄清楚其他的事情。在这种情况下，你所拥有的数据只是一个模糊的目标概念。你的计算器草图给了你一些关于你需要建模的“现实”的信息，但不是一个完整的描述。你的问题比答案还多。

作为开发人员，您需要使用您所拥有的信息来创建适当的抽象。一个有用的抽象是允许实现一个简单的模型来满足最初的需求，并给出空间来改进该模型，直到它接近理想状态。

概括地说，你的领域是计算器的世界。你的目标是定义和建立理想的模型；然而，你不知道那看起来像什么。你有的只是一张写在餐巾纸背面的模模糊糊的草图。

## 你的目标是参差不齐的墨水飞溅

打个比方，想象一下理想的模型是这样的黑色墨水飞溅:

[![](img/5c1ac8d93da1bddabd2ccc2bd532ccb0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1f2BaJtB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dzbr3c1cw82lapz3uf99.png)

splatter 代表了完美解决方案的功能、特性和行为。理想的解决方案是一个模型，它使用抽象的所有方面来满足您试图解决的问题的所有需求，包括现在和将来的需求。

飞溅的锯齿状边缘代表了理想抽象的复杂性和细节。形状很复杂，有许多方面很难正确建模。

### 过分简单化

第一个简单的近似方法可以是一个模拟计算器的应用程序，如下所示:

[![](img/fdfea99dd9d7399b735e4f44bdad9a82.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s---qr2wjkY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3jdv7p26nrgwja7kwq12.png)

这是一个开始，但它只涵盖了理想目标的一小部分。下面的图片显示了在黑色墨水飞溅比喻的背景下的小部分；以蓝色显示:

[![](img/ccd4deaa9d8991a42dc717b17088e2fc.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3iSLjXIn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/sgo2e84eu6pmiwq28iy8.png)

顾客不会对它满意的。这个模型太简单了，它只涵盖了理想的几个特征。

### 简化与偏离

第二次尝试可以扩展这个想法，但是在这个过程中，你可能会做出一些偏离理想概念的选择；例如，您可以选择添加一些新按钮和一个使用工作流程，这并不是客户想要的。结果可能类似于以下模型:

[![](img/f3050e3b64f6b32d0f5e16e977a02abf.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--1HTJJVRB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9hg5saehgbr58irosiiw.png)

与过度简化相比，该模型覆盖了更多的原始黑色墨水飞溅，但它也扩展到了理想的边界之外。

[![](img/f58bcd12c884776856363f4337bdd2ab.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sD2hGfyy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0f3un9p86epndlpxcsmi.png)

总的来说，这个模型并不完整，而且有点偏离。

### 一种可以接受的简化

经过几次迭代，对原始草图的仔细研究，以及与客户的大量交谈，您可以得到理想模型的更好的近似；它并不完美，但它抓住了大部分需要的特征。它缺少一些按钮和功能，但它增加了一个客户喜欢的更大的屏幕。

[![](img/cba48de6a52d93b30b94284fe31888d0.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--IFr_BQrO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/umqudlhwsu4m5wde7pb2.png)

这种近似比以前的迭代更接近理想解。在黑色墨水飞溅的顶部用蓝色可视化，这个完美抽象的模型覆盖看起来像这样:

[![](img/c1338e66d832f8395c3cbb94b32e6775.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--SDu4f-Go--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zl0fvndux7ebv3a5bvml.png)

### 过度抽象

过度简化是不可接受的，但是过度工程化和过度抽象更危险。当你把事情变得比理想的解决方案复杂得多时，你就落入了那个陷阱，增加了不必要的成本，错过了最初目标的精神。

因此，如果你被要求制造一个计算器，你可能会得到一台超级计算机。

[![](img/3db2155a4b6eec651fec8573e84ed25d.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--d2c8m94J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/af664vcqjqmijpbmy1rl.jpg)

用墨水的比喻来说，它覆盖了整个黑色飞溅物和周围一大块可观测的宇宙。

[![](img/d919d21cdc562fd94fb38e0e68c02c95.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--3OCQ8HzL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l26h1jnh952hs5kpavke.png)

在这个夸张的例子中，模型变得比理想的抽象大得多，这意味着你过度抽象了问题。

# 选择正确的抽象和初始模型

当你面对一个问题时，你需要仔细考虑正确的抽象层次。目标是创建一个:

*   可以模拟一个初始解决方案来满足绝对最低要求。
*   可以提炼为新的模型，以可接受的成本解决已知的可预见的未来需求。
*   不要试图解决你永远不需要解决的问题。

为了评估一个抽象并选择初始模型，您需要有两个列表:

1.  最低要求清单；需要此列表来确定正确的初始模型。
2.  所有可预见的未来需求清单；需要这个列表来选择合适的抽象。

当评估一个抽象和基于它的初始模型时，问自己以下问题:

1.  模型满足最低要求列表吗？如果不是，则模型过于简单，需要改进。也就是说，您需要扩展黑色墨水飞溅上的初始蓝色区域。
2.  抽象是否允许模型的最终扩展以合理的成本覆盖可预见的未来需求？如果不是，您可能需要扩展您的抽象。也就是说，您可能需要扩大域中黑色油墨飞溅覆盖的区域。
3.  这个模型是否解决了更多不在这两个列表中的问题？如果是这样，您可能已经创建了一个过度抽象。考虑减少。过度抽象会增加长期成本。

## 结论

选择适当的抽象层次是一门科学，也是一门艺术，你需要通过经验来学习。我希望这篇论文能帮助你想象这个挑战，并给你一些额外的工具来提高你的模型抽象技能。

* * *

### 如果你喜欢这篇文章，请保持联系！

*   在 CoderHood 上找到我所有的帖子。不要忘记订阅邮件来接收新帖子的通知。
*   在 LinkedIn 上加入我的职业网络。
*   在推特上关注我。
*   加入我的脸书主页。
*   最后，请在 dev.to 上关注我！