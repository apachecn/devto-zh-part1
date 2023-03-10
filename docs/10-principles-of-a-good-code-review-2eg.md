# 优秀代码评审的 10 个原则

> 原文：<https://dev.to/codemouse92/10-principles-of-a-good-code-review-2eg>

**编辑:**就像代码审查本身一样，我的同事在评论区和 Twitter 上提出了一些非常好的观点。如果你已经读过这篇文章，请看我在`EDIT`部分的笔记。

另外，请阅读菲利普·豪尔的[代码审查指南](https://blog.philipphauer.de/code-review-guidelines/)

* * *

几乎任何健康的编程工作流程都会在过程中的某个时候涉及到**代码审查**。这可能是 GitHub 上的 Pull 请求，Phabricator 上的 Differential Revision，Atlassian 上的 Crucible Review，或者任何数量的其他评审工具。但是不管你怎么做，并不是所有的代码评审都是平等的。

在 [MousePaw Media](https://mousepawmedia.com/) ，我们有一个严格执行的工作流程，其中包括强制性的提交前代码审查。这些帮助我们捕获了许多错误和次优代码。

然而，许多实习生害怕做代码评审，害怕他们没有什么贡献，尤其是当评审由已经在那里工作了很长时间的开发人员编写的代码时！与此同时，代码评审的质量——甚至是我自己的——可能会有很大的不同，这取决于许多因素:对代码的熟悉程度、一天中的时间、一天中的时间，等等。

我已经将过去几年中关于代码评审的想法和笔记编辑成了一个指南，并发表在我们的员工网络文档上。我想分享结果(稍微适应 dev.to)。

该表扬的我一定要表扬！我从 Scott Nonnenberg 的 *[十大拉请求评审错误](https://blog.scottnonnenberg.com/top-ten-pull-request-review-mistakes/)* 、Jeff Atwood 的 *[对你的代码做可怕的事情](https://blog.codinghorror.com/doing-terrible-things-to-your-code/)* 以及 Sam Jarman 的 *[给予和接受伟大的代码评审](https://dev.to/samjarman/giving-and-receiving-great-code-reviews)* 中汲取了很多灵感。

* * *

# 谁可以审核？

正如我所说的，审查别人的代码有时是令人畏惧的，特别是如果那个人比你有更多的经验、专业知识或资历。但是不要害怕！当每个人都参与代码评审时，每个人都是赢家！这里有一些需要记住的有用的事情。

首先，每个人都会犯错，我们也知道！当一个程序员知道他或她将被审查代码时，这就像一个安全网:他们可以更容易地放松和编码，知道在代码被认为“完成”之前，另一双眼睛将会阅读这些代码。

第二，每个人都从代码审查中学习。作者获得了关于如何改进他们的代码的额外见解；评审者可以从他们评审的代码中学习新的技术和思想；旁观者既得到了*又得到了*的好处。

总之，**不要害怕贡献反馈**！代码审查可能是您对项目做出的最有价值的贡献之一。

也就是说，什么是好的评估？

* * *

# 原则 1

一个好的评审的首要原则是:**如果你承诺评审代码，就彻底评审它！**期望在这上面花费大量时间。务必*阅读*代码，不要只是略读，要对代码及其风格都进行思考。

一般来说，如果你找不到任何具体的东西来指出，要么代码是完美的(几乎从来不是真的)，要么你错过了一些东西。因此，您可以使用它作为一个相当准确的度量，来衡量您审查代码的好坏。

目标是在最初的评审中总是建议至少*个对代码(不仅仅是风格)的具体改进。后续审查可能不需要这样做；否则我们永远不会登陆密码！*

**编辑:**尤其是代码改动小的情况下，虚拟完美是绝对有可能的。可能有些评审根本不需要任何改变，但是你应该相信你付出了努力来真正得出这个结论。

# 原则二

这与第二个原则密切相关:**旨在理解每一个改变的行**。研究你不懂的东西。提问。

这一点之所以重要，有三个主要原因:

1.  在真正优雅的代码中，简单通常比复杂好。了解最佳解决方案是否正在使用的唯一方法是*了解*当前的解决方案。

2.  其他人可能需要阅读这段代码。如果你在理解代码上有困难，它可能需要重构，清理，或者更好的注释。

3.  你掌握的知识越多，你的代码和评论就越好！

完成后，你应该能够自己回答以下两个问题:

*   "这段代码的目的是什么？"

*   “它是如何实现这个目标的？”

如果你不能回答这两个问题，你就没有完全理解这些变化！

**编辑:**你不一定要了解整个代码库。一般来说，您应该假设未更改的代码是有效的，并且只需回顾一下，以确认它在更改后的代码中被正确使用。

# 原则三

不要假设代码可以工作——自己构建并测试它！你实际上应该把代码拉下来测试一下。在 Phabricator Differential 上，提交用于提交前审查的代码包括来自作者的测试计划。

当然，在测试代码时，要确保你的构建是正确的。如果项目有构建系统，您应该能够使用它。如果持续集成系统报告成功地构建了代码，您也应该能够。

关于这一点，如果 CI 构建*失败*，您应该要求代码成功构建后才能登陆！

一旦你编译了代码，*实际测试它*。在 MousePaw Media，我们的大多数项目都有一个为任意代码提供空间的测试器；你可以用这个来试试。

您还应该运行包含的自动测试，不要停留在这一步。**尝试破解密码！**如果您最终发现自动测试可以更好地覆盖的案例，建议在测试中考虑这些案例。

再一次，看看杰夫·阿特伍德写的 *[对你的代码做了可怕的事情](https://blog.codinghorror.com/doing-terrible-things-to-your-code/)* ，以获得好的测试技巧。

**编辑:**我可能强调得不够，但是*相信 CI* 。这里的目的是在自动单元测试之外测试代码；简而言之，您正在测试 CI 无法测试的内容。如果这不适用，并且真的没有什么*可以*手动测试，不要浪费你的时间。

# 原则四

**评头论足。** MousePaw Media 开发并使用了[注释显示意图标准](https://standards.mousepawmedia.com/csi.html)，这意味着几乎每个逻辑语句都应该有一个注释来描述程序员的意图。(参见我的文章 *[你的项目还没有完成](https://dev.to/codemouse92/your-project-isnt-done-yet)* 来解释为什么意图注释很重要。)

假设您正在处理一个遵循这个约定的项目，如果您没有看到意图注释，您应该请求将一个添加到代码中。那就认准了再批。(如果项目*没有*遵循 CSI 标准或类似的标准，考虑提议对所有未来的代码采用该标准。)

关于评论，仅仅有一些东西是不够的。意图注释应该实际描述*意图*。您应该解决以下任何问题:

1.  **意图注释与逻辑不符。**这表示注释、代码或两者都是错误的。我们已经用这种方法抓住了*许多*潜在的讨厌的虫子！

2.  **意图评论没有意义。**如果评论很混乱，那就和完全没有评论一样有用。

3.  **意向评论有重大错别字。语法和拼写对意义很重要，尤其是当一个人不了解听众的时候。下一个读者会是英语为第二语言的人吗？诵读困难？刚学编码？地道的英语总是最容易理解的。**

# 原则五

像审查生产代码一样严格地审查临时代码。令人震惊的是，临时“补丁”代码和变通方法投入生产的频率之高，以及其中有多少从未被真正替换。这只是现实世界编程的一个现实。因此，我们应该用同样的标准和期望来看待*所有的*代码。

换句话说，即使代码的解决方案并不理想，实现也应该是干净的、可维护的和相当高效的。

换句话说，没有任何借口可以编写蹩脚的代码。

# 原则六

考虑代码在生产中如何工作。设计很重要，集成也很重要。这段代码在现实世界中将如何运行？它将如何处理错误的输入和用户错误？它会和其他代码库一起运行吗？简而言之，对代码要求严格。(参见原则 3。)

这符合原则 5。请求(作为作者)或授予(作为评审者)对“未完成的”代码的宽限是很诱人的，但是这里面隐藏着发布破损代码的严重危险！

如果代码*被*破解，用户一般不应该轻易获取！未完成的类可以被标记为“实验性的”,并被记录为“实验性的”,从而防止用户将其误认为已完成的代码。相比之下，一个坏的函数不应该在非实验类中公开。

看待这件事的另一种方式是:**如果代码在下一次提交时被交付给最终用户，它可能是*功能不完整的*，但是它不应该是*损坏的*。**在现实中，这个目标很少实现，但是透视图将有助于防止坏代码进入您的存储库。

# 原则#7

检查文档、测试和构建文件。好的代码不仅仅包括代码，它还包括所有伴随它的装饰。(再看 *[你的项目还没做完](https://dev.to/codemouse92/your-project-isnt-done-yet)* 。

在 MousePaw Media，我们希望每个版本都包含以下所有内容:

*   覆盖新代码的测试。像审查代码本身一样严格地审查这些，以确保如果有问题，测试将会失败。

*   新代码的文档。最好的文档是与代码本身一起编写的。不接受文档*后来*；它应该出现在修订本身中！

*   **为变更更新构建文件。任何时候添加、删除或重命名代码文件，构建文件都需要反映这些变化。类似地，如果任何依赖项发生了变化，构建文件也应该反映出来。这是您应该自己构建变更的另一个原因(原则 3)。**

*   **自述变更。**降价文件，如`README.md`、`BUILDING.md`、`CHANGELOG.md`等应反映最新的变化。实际上，这些很少需要更改，但是您应该确保它们是最新的。

# 原则 8

回顾时，**提建议时保持轻重缓急。**

代码应该是...

1.  功能至上，

2.  第二，清洁和可维护

3.  优化三。

代码最终应该实现这三个目标，但是顺序很重要。如果代码不工作，先不要担心样式。类似地，如果代码被破坏或者风格不佳，优化只会让事情变得更糟。

# 原则 9

**跟进评论。**提出修改建议后，要做好再次审核的准备。确保进行了必要的更改，并合理地解决了您发现的任何问题。

一定要像关注最初的回顾一样关注后续的回顾！重新应用所有十项原则。

# 原则 10

复习是令人畏惧的，所以记住**复习者并不完美是有帮助的！**问题可能会从你身边溜走，错误可能会逃避检测，性能缺陷可能会影响生产...简而言之，代码损坏时有发生！

如果您不熟悉代码或概念，您可能希望请求其他评审人员提供反馈，但是不要回避自己进行评审！归根结底，四只眼睛总比两只好。

如果你确实意识到自己在评估中犯了错误，你能做的最好的事情就是承认错误。如果合适，提出对提交后审查系统的关注，或者提交一份问题/错误报告。

一位推特评论员从另一个角度指出了这个原则:**让你的自我远离评论！**这不是一个高人一等的竞技场。如果你带着展示你的才华的意图去做，推倒另一个编码员，或者用你的经验击败他们，那就帮所有人一个忙，根本不要费心审查代码。**一个带有自我意识的代码评审比根本没有评审要糟糕得多。**

# 多一件事...

在 MousePaw Media，我们实际上有一个*严格的*修订清单。一切都有望达到所有这些目标。显然，这是为我们的特定项目量身定制的，但你也许可以为此做些笔记，并提出你自己的想法。

当我们第一次开发这个清单的时候，我还没有找到布莱恩·奥斯普丘克的 *[防止愚蠢错误的代码审查清单](https://dev.to/bosepchuk/a-code-review-checklist-prevents-stupid-mistakes-o6)* ，但是它非常值得一读！

因此，对我们来说，每次修订都必须...

(1)实现其设计目标。

我们遵循每个版本一个特性的规则。在某些情况下，功能本身可能会被删除，而只有错误修复和/或优化。

(2)已经将来自`master`的所有改变合并到其自身中，并且所有冲突都已解决。

(3)不跟踪和移除二进制文件和不必要的垃圾文件。(持续关注`.gitignore`！)

(4)正确编译和运行——这应该通过 CI 系统(在我们的例子中是 Harbormaster/Jenkins)来确认。

(5)没有编译器错误和警告。

为了#5 的目的，我们用`-Wall -Wextra -Werror`编译我们所有的 C++代码。

(Valgrind 纯(没有检测到内存泄漏)。

同样，这是我们的 C 和 C++代码所特有的，但是许多语言都有对等的东西。

(7)符合公司(或项目)的编码和技术标准。

(8)没有棉绒错误。

我们用`cppcheck`表示 C++，用`pylint`表示 Python。

(9)得到老何的充分评论。

再次，看我们的[评论展示意图标准](https://standards.mousepawmedia.com/csi.html)。

(10)如果相关的话，有一个最新的构建脚本(在我们的例子中是 CMake)。

(11)包含相关测试。

(12)有一个测试计划来帮助审查者确保你的代码工作。

(13)由至少一名受信任级别的审查者进行审查、构建、测试和批准。

(14)拥有最新的(Sphinx)文档，编译时没有警告。

我们通过*而不是*把这件事推迟到以后来得到最好的结果！

(15)处理所有审核人的意见并标记“完成”。

这是我们特殊的评审工具 Phabricator Differential 的副作用，但是您可能会要求阅读并考虑所有建议的更改。

# 总结

这些原则对 MousePaw Media 的工作流程来说并不陌生——我们已经暗中遵循了一段时间——但是我希望通过精心制作这个指南，我们可以更一致地应用它们。

您的项目或组织遵循什么代码评审原则？

编辑:请阅读评论部分，尤其是 edA-qa mort-ora-y 的精彩评论(以及由此引发的对话)。关于这一主题，有一些有价值的笔记和替代观点值得考虑。)